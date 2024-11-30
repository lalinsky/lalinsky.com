---
layout: post
title: Msgpack serialization library for Zig
categories:
- programming
tags:
- programming
- zig
- msgpack
---

I've been playing with Zig over the last few weeks. The language had been on my radar for a long time, since it was originally developed for writing audio software,
but I never paid too much attention to it. It seems that it's becoming more popular, so I've decided to learn it and
picked a small task of rewriting the AcoustID fingerprint index server in it. That is still in progress, but there is one side product that is almost
ready, a [library for handling msgpack serialization](https://github.com/lalinsky/msgpack.zig).

The library can be only used with static schemas, defined using Zig's type system. There are many options for generating compact messages, almost competing with protobuf,
but without separate proto files and protoc. I'm quite happy with the API. It's mainly possible due to Zig's comptime type reflection.

This is the most basic usage:

```zig
const std = @import("std");
const msgpack = @import("msgpack");

const Message = struct {
    name: []const u8,
    age: u8,
};

var buffer = std.ArrayList(u8).init(allocator);
defer buffer.deinit();

try msgpack.encode(Message{
    .name = "John",
    .age = 20,
}, buffer.writer());

const decoded = try msgpack.decodeFromSlice(Message, allocator, buffer.items);
defer decoded.deinit();

std.debug.assert(std.mem.eql(u8, decoded.name, "John"));
std.debug.assert(decoded.age == 20);
```

See the project on [GitHub](https://github.com/lalinsky/msgpack.zig) for more details.
