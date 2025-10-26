---
layout: post
title: How I turned Zig into my favorite language to write network programs in
categories:
- programming
tags:
- zig
- networking
- async
---

I've been watching the [Zig](https://ziglang.org/) language for a while now, given that it was created for
writing audio software (low-level, no allocations, real time). I never paid too much attention though,
it seemed a little weird to me and I didn't see the real need. Then I saw a post from [Andrew Kelley](https://github.com/andrewrk)
(creator of the language) on Hacker News, about how he reimplemented my Chromaprint algorithm in Zig, and that got me really interested.

I've been planning to rewrite AcoustID's inverted index for a long time, I had a couple of prototypes, but none of the approaches felt right.
I was going through some rough times, wanted to learn something new, so I decided to use the project as an opportunity to learn Zig.
And it was great, writing Zig is a joy. The new version was faster and more scalable than the previous C++ one.
I was happy, until I wanted to add a server interface.

In the previous C++ version, I used [Qt](https://www.qt.io/), which might seem very strange for a server software, but I wanted a
nice way of doing asynchronous I/O and Qt allowed me to do that. It was callback-based, but Qt has a lot of support for
making callbacks usable. In the newer prototypes, I used Go, specifically for the ease of networking and concurrency.
With Zig, I was stuck. There are some Zig HTTP servers, so I could use those. I wanted to implement my legacy TCP server as well,
and that's a lot harder, unless I want to spawn a lot of threads. Then I made a crazy decision, to use Zig also for implementing
a clustered layer on top of my server, using NATS as a messaging system, so I wrote a [Zig NATS client](https://github.com/lalinsky/nats.zig),
and that gave me a lot of experience with Zig's networking capabilities.

Fast forward to today, I'm happy to introduce [Zio, an asynchronous I/O and concurrency library for Zig](https://github.com/lalinsky/zio).
If you look at the examples, you will not really see where is the asynchronous I/O, but it's there, in the background and that's
the point. Writing asynchronous code with callbacks is a pain. Not only that, it requires a lot of allocations, because you need
state to survive across callbacks. Zio is an implementation of Go style concurrency, but limited to what's possible in Zig.
Zio tasks are stackful coroutines with fixed-size stacks. When you run `stream.read()`, this will initiate the I/O operation in the background
and then suspend the current task until the I/O operation is done. When it's done, the task will be resumed, and the result will be returned.
That gives you the illusion of synchronous code, allowing for much simpler state management.

Zio support fully asynchronous network and file I/O, has synchronization primitives (mutexes, condition variables, etc.) that work with the cooperative runtime,
has Go-style channels, OS signal watches and more. Tasks can run in single-threaded mode, or multi-threaded, in which case they can migrate from thread to thread
for lower latency and better load balancing.

And it's FAST. I don't want to be posting benchmarks here, maybe later when I have more complex ones, but the single-threaded mode is beating any framework I've tried so far.
It's much faster than both Go and Rust's Tokio. Context switching is virtually free, comparable to a function call. The multi-threaded mode,
while still not being as robust as Go/Tokio, has comparable performance. It's still a bit faster than either of them, but that performance
might go down as I add more fairness features.

Because it implements the standard interfaces for reader/writer, you can actually use external libraries that are unaware they are running within Zio. Here is an example of a HTTP server:

```zig
const std = @import("std");
const zio = @import("zio");

const MAX_REQUEST_HEADER_SIZE = 64 * 1024;

fn connectionTask(rt: *zio.Runtime, stream: zio.net.Stream) !void {
    defer stream.close(rt);

    var read_buffer: [MAX_REQUEST_HEADER_SIZE]u8 = undefined;
    var reader = stream.reader(rt, &read_buffer);

    var write_buffer: [4096]u8 = undefined;
    var writer = stream.writer(rt, &write_buffer);

    var server = std.http.Server.init(&reader.interface, &writer.interface);

    while (true) {
        var request = try server.receiveHead();
        try request.respond("hello", .{ .status = .ok });

        if (!request.head.keep_alive) break;
    }
}

fn serverTask(rt: *zio.Runtime) !void {
    const addr = try zio.net.IpAddress.parseIp4("127.0.0.1", 8080);

    const server = try addr.listen(rt, .{});
    defer server.close(rt);

    while (true) {
        const stream = try server.accept(rt);
        errdefer stream.close(rt);

        var task = try rt.spawn(connectionTask, .{ rt, stream }, .{});
        task.deinit();
    }
}

pub fn main() !void {
    var gpa = std.heap.GeneralPurposeAllocator(.{}){};
    defer _ = gpa.deinit();
    const allocator = gpa.allocator();

    var runtime = try zio.Runtime.init(allocator, .{});
    defer runtime.deinit();

    try runtime.runUntilComplete(serverTask, .{&runtime}, .{});
}
```

When I started working with Zig, I really thought it's going to be a niche language to write the fast code in, and then I'll need a layer on top of
that in a different language. With Zio, that changed. The next step for me is to update my NATS client to use Zio internally. And after that,
I'm going to work on a HTTP client/server library based on Zio.

