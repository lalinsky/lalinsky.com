---
layout: post
title: Async I/O in Zig 0.16, today
categories:
- programming
tags:
- zig
- networking
- async
---

[Zig 0.16][zig-016] shipped last month with [`std.Io`][std-io], a cross-platform
interface for I/O and concurrency. This is a big step for the ecosystem.
Libraries can now be written against a standard I/O abstraction, independent
of the runtime, and application developers can plug in whatever implementation
they want.

The only usable implementation shipped with 0.16 is `std.Io.Threaded`,
which uses a thread pool. When you spawn concurrent tasks, it creates
OS threads to run them. Let's see how it works with a simple example:

```zig
const std = @import("std");

const num_tasks = 10_000;

fn task(io: std.Io) std.Io.Cancelable!void {
    try io.sleep(.fromSeconds(10), .awake);
}

pub fn main(init: std.process.Init) !void {
    var group: std.Io.Group = .init;
    for (0..num_tasks) |_| {
        try group.concurrent(init.io, task, .{init.io});
    }
    try group.await(init.io);
}
```

This spawns 10,000 concurrent tasks, each sleeping for 10 seconds.
On my machine, it completes in about 20 seconds:

```
$ time ./std_demo

real    0m20.158s
user    0m2.258s
sys     0m10.098s
```

The overhead comes from spawning OS threads. If you try increasing
this to 50,000 tasks, it will likely fail on most systems due to
thread limits (`ulimit -u` on Linux).

This isn't just an arbitrary benchmark. Asynchronous I/O exists to
solve a real problem: network servers with many connected clients.
You don't want to spawn an OS thread for every client connection.
That's why we have event loops, coroutines, and async I/O.

There is `std.Io.Evented` in the standard library, which is meant
to use io_uring on Linux and kqueue on BSD/macOS. It's still a work
in progress though, missing many functions and doesn't currently
compile.

I've written about [zio][zio] [before][zio-post], and I've just released version 0.11
with a full `std.Io` implementation. It uses stackful coroutines
and asynchronous OS-level I/O APIs (io_uring or epoll on Linux,
kqueue on BSD/macOS, IOCP on Windows). Here's the same example
using zio:

```zig
const std = @import("std");
const zio = @import("zio");

const num_tasks = 10_000;

fn task(io: std.Io) std.Io.Cancelable!void {
    try io.sleep(.fromSeconds(10), .awake);
}

pub fn main(init: std.process.Init) !void {
    const rt = try zio.Runtime.init(init.gpa, .{});
    defer rt.deinit();

    const io = rt.io();

    var group: std.Io.Group = .init;
    for (0..num_tasks) |_| {
        try group.concurrent(io, task, .{io});
    }
    try group.await(io);
}
```

The code is almost identical. You just initialize a zio runtime
and use its `io()` method to get the `std.Io` interface. With zio,
the same 10,000 tasks complete in about 10 seconds:

```
$ time ./zio_demo

real    0m10.606s
user    0m3.136s
sys     0m7.126s
```

That's the expected time, since all tasks run truly concurrently.
You can increase this to 50,000 or more tasks and it will continue
to work, limited only by available memory.

You can use this `io` instance for anything you'd use `std.Io.Threaded` for.
To write an HTTP server with `std.http.Server`, for example, just pass zio's
`io` and it will work the same way.

If you want to use async I/O in Zig 0.16 with the standard APIs,
you don't need to wait for `std.Io.Evented` to be ready. Zio's
implementation is still new, so if you hit any problems, please
reach out on [GitHub][zio] and I'll be happy to help.

[zig-016]: https://ziglang.org/news/0.16.0-released/
[std-io]: https://ziglang.org/download/0.16.0/release-notes.html#IO-as-an-Interface
[zio]: https://github.com/lalinsky/zio
[zio-post]: /2025/10/26/zio-async-io-for-zig.html
