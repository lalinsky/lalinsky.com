---
layout: post
title: Fast JSON and MessagePack in Zig
categories:
- programming
tags:
- zig
- json
- msgpack
- serialization
- performance
---

My first Zig project ever was [msgpack.zig](https://github.com/lalinsky/msgpack.zig). I had just started rewriting the [AcoustID index](https://github.com/acoustid/acoustid-index) in Zig, and I needed a binary format for it. In C++, I used to use protobuf, but I didn't want to generate Zig code from a `.proto` schema. Zig already has a good way to describe data: its own structs. So I settled on [MessagePack](https://msgpack.org/), with the protocol defined by Zig types. I still wanted some control over how things are serialized, though. In particular, I wanted protobuf-style numbered fields, so that messages could be compact without losing forward compatibility.

JSON came later. I prefer a text format for my internal services, so I didn't want to use msgpack there. From other languages, I was also used to JSON serialization being faster than I would expect. That was not the case with `std.json`, so I started applying the lessons I learned from msgpack.zig to JSON and that resulted in [json.zig](https://github.com/lalinsky/json.zig).

Both libraries ended up with the same basic design. They only work with statically known Zig types, and customization is done through options on those types rather than custom parsing code. This makes them less flexible than a general-purpose parser, but also lets them do much less work.

To explain what I mean by that, I'll start with how `std.json` works.

## The tokenizer problem

`std.json` can decode directly into your types, with `std.json.parseFromSlice(T, ...)`, without a generic `Value` in between. But everything still goes through a tokenizer. The `Scanner` turns the input into a stream of tokens, and the typed parser consumes those tokens. When it's decoding a struct, it reads the next key as a string token, and then compares it against each field name, one by one, until it finds a match. Then it recursively parses the value, again from tokens.

This makes it slow, because every key and every value passes through the token layer, and the parser can't use what it knows about the type to skip any of that work. It also makes customization hard. The token stream is the only interface, so if you want anything that doesn't map 1:1 to your struct, you have to write a `jsonParse` function that consumes tokens and rebuilds the struct by hand. Renaming a single field means writing the parser and the serializer for the whole struct.

My libraries don't have a tokenizer. The function that decodes a struct is generated at compile time from that struct's fields, and it reads the bytes straight into the result.

## Static types

You define a struct, and that's your schema:

```zig
const std = @import("std");
const json = @import("json");

const Message = struct {
    name: []const u8,
    age: u8,
};

try json.encode(Message{ .name = "John", .age = 20 }, writer);
// {"name":"John","age":20}

const message = try json.decodeFromSliceLeaky(Message, arena, bytes, .{});
```

The msgpack version looks the same, just with `msgpack` instead of `json`.

Encoding writes to a `std.Io.Writer`, and decoding reads from a `std.Io.Reader`, so they work against sockets and files, not just buffers in memory. The decoder also doesn't need the whole document in its buffer; it streams. A larger buffer will obviously perform better, but a 1 MB string can still be decoded through a 64-byte buffer.

There is no `Value` type. If you don't know the schema, use `std.json`. But for APIs with strict schemas, which is most of what I do, knowing the type up front is what allows the decoder to be fast.

## Configuration, not code

Since the code is generated from the types anyway, a type can declare how it wants to look on the wire, and the generated code takes that into account.

Renaming fields is the most common case. Most APIs use camelCase, and I don't want to use camelCase in Zig:

```zig
const User = struct {
    user_id: u32,
    nickname: ?[]const u8,

    pub fn jsonFormat() json.StructOptions {
        return .{ .key = .custom };
    }

    pub fn jsonFieldName(comptime field: std.meta.FieldEnum(@This())) []const u8 {
        return switch (field) {
            .user_id => "userId",
            .nickname => "nickname",
        };
    }
};
```

By default, optional fields that are `null` are left out of the output, which is what I normally want for APIs. If you need the `null`, set `omit_null_fields = false`. Unknown fields are an error by default, but you can skip them with `skip_unknown_fields`, either on the type or on the whole decode. Because these options belong to the type, they apply wherever the type ends up nested, which is not the case with `std.json`, where the options apply to the whole call.

Tagged unions are the other common case. By default, a union is encoded as a one-member object, `{"circle":{"radius":2.5}}`, but most real-world APIs put a discriminator field next to the data, and json.zig supports that directly:

```zig
const Shape = union(enum) {
    circle: struct { radius: f64 },
    rect: struct { w: u32, h: u32 },

    pub fn jsonFormat() json.UnionFormat {
        return .{ .as_tagged = .{ .tag_field = "type" } };
    }
};
```

```json
{"type":"circle","radius":2.5}
```

The tag has to be the first member. Otherwise, the decoder would have to buffer the whole object before knowing which variant to build, and that's not compatible with streaming.

In msgpack.zig, the options are more about compactness. Struct keys can be field names, field indexes, or your own numeric keys. The numeric keys are what I wanted from protobuf: you can rename and reorder fields without breaking existing data.

```zig
const Message = struct {
    name: []const u8,
    age: u8,

    pub fn msgpackFormat() msgpack.StructFormat {
        return .{ .as_map = .{ .key = .custom, .skip_unknown_fields = true } };
    }

    pub fn msgpackFieldKey(field: std.meta.FieldEnum(@This())) u8 {
        return switch (field) {
            .name => 1,
            .age => 2,
        };
    }
};
```

With `skip_unknown_fields`, old consumers can also read messages from newer producers that added fields.

## Fast

Most of the speed comes from simply doing less work. Keys are matched against field names known at compile time, numbers are parsed in the same pass that finds their end, and strings are copied straight into the result.

In the [GLD serializer benchmark](https://leo-gan.github.io/GLD.SerializerBenchmark/dashboard/?lang=zig&dataset=suite&data=all%40100&mode=published&metric=ops&baseline=std.json&cbase=zig%7Ccomptime-bin&ser=zig%7Ccomptime-bin&ser=zig%7Cflatbuffers&ser=zig%7Cs2s&ser=zig%7Cmsgpack.zig&ser=zig%7Cserde.msgpack&ser=zig%7Cprotobuf&ser=zig%7Cjson.zig&ser=zig%7Cgld-avro), which compares Zig libraries, json.zig decodes 2-4x faster than `std.json`, depending on the use case, and msgpack.zig is ahead of the other Zig msgpack libraries.

I also have my own benchmark against the best C++ and Rust libraries, but comparing across languages is hard, so I haven't published it yet. Allocation alone can make a 2x difference. My libraries decode into an arena, while C++ and Rust libraries typically allocate every string separately. When I account for that, both libraries are comparable to glaze, simdjson and sonic-rs. Currently, floating-point numbers are the main weak spot of my JSON library.

## Using them

Both libraries work with Zig 0.16 and 0.17:

```bash
zig fetch --save "git+https://github.com/lalinsky/json.zig?ref=v0.2.0"
zig fetch --save "git+https://github.com/lalinsky/msgpack.zig?ref=v0.10.0"
```

msgpack.zig has been used in production for a while now. json.zig is newer and hasn't seen production traffic yet. There are use cases I haven't run into myself, so if something doesn't work for you, open an issue on GitHub.
