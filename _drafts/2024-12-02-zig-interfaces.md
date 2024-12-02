---
layout: post
title: "Missing parts of Zig: Interfaces"
categories:
- programming
tags:
- programming
- zig
---

If there is one thing that I'm missing in Zig, it's a way to describe a abstract type. One of the most powerful features of Zig are comptime computations, which
allows for generic types just by having a function that returns a new type based on the function's parameters. That is great, but there are many times,
when you don't want to expose the full type signature to all the functions that need to work with the generic value.

```zig
pub const Reader = interface {
    pub fn read(self: *Reader, buffer: []u8) anyerror!usize;
};

var new_reader = std.meta.wrapInterface(&reader);
```
