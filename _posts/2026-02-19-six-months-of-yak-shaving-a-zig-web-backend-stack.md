---
layout: post
title: Six months of yak shaving a Zig web backend stack
categories:
- programming
tags:
- zig
- networking
- async
---

A while back I [wrote about][zio-post] [Zio], my async I/O library
for Zig.
At the end of that post I said the next step was to update my NATS
client and write an HTTP server. Well, one thing led to another,
and I now have a whole web backend stack written entirely in Zig.

It started with NATS. I wanted to cluster my server, NATS seemed like
a good fit, and I thought: how hard would it be to write a Zig client?
Turns out, not that hard. [nats.zig] came together well, but it was
using threads and blocking sockets. I felt like I stepped into
the past, I really wanted asynchronous I/O for this.
Every approach I tried (wrapping libuv, wrapping libxev) ended up
requiring constant allocations and reference counting.
It was not nice code. I really wanted Go-style networking APIs.

So I asked myself the stupid question again, how hard could *that* be?
And that led to a fun, but very long journey.
I've already [written about it][zio-post].

With Zio working, I needed an HTTP server. I was previously using [http.zig] by Karl
Seguin, which is a great project, but it runs its own event loop and
I wanted multiple network services sharing the same runtime. So I wrote
[Dusty], with an API inspired by http.zig but built on Zio. Dusty is
more than just an HTTP server, it's a full HTTP package. It
includes an easy to use HTTP client with connection pooling, natively
supports WebSocket (both server and client), Server-Sent Events, has
a router with parameter and wildcard support, and a middleware system.
I used llhttp for parsing rather than writing my own, because while
HTTP/1.1 is an easy to read protocol, there are many edge cases and
why not use something that already covers them all.

At this point I had a runtime and an HTTP server, which meant I could
actually build something. I still needed some more client libraries.
The first one was for [Memcached][memcached.zig]. That was fairly easy
to build, it uses the same rendezvous hashing algorithm as
the Python client library, I was happy with it.
Then came the [PostgreSQL][pg.zig] client.
I adapted [pg.zig](https://github.com/karlseguin/pg.zig) to use my own I/O layer,
it was a small change and worked well.
And then I took the Memcached client, removed hashing, replaced the protocol parser,
and turned it into a small [Redis][redis.zig] client.
This one is very far from feature-complete, but I don't use Redis
for much, so it covers just what I needed.

I never expected to be writing networking code in Zig when I started.
But it turns out I now have a stack I actually enjoy working with.
Maybe I need to stop the yak shaving at this point, and finish the
AcoustID project that started it all.

Writing backends in Zig is really not for everything. If you're building
a CRUD app, Go or Python will get you there faster. But for the
kind of work where performance matters at a low level, like databases,
streaming services, or audio processing, I now consider Zig a good option.
You don't have to drop down to a different language for the networking
layer.

*Once Zig 0.16 is released, I'll probably work on migrating the
libraries to use `std.Io`, but there is still some missing
functionality in the APIs that makes it impractical at this point.*

[zio-post]: https://lalinsky.com/2025/10/26/zio-async-io-for-zig.html
[Zio]: https://github.com/lalinsky/zio
[Dusty]: https://github.com/lalinsky/dusty
[nats.zig]: https://github.com/lalinsky/nats.zig
[pg.zig]: https://github.com/lalinsky/pg.zig
[redis.zig]: https://github.com/lalinsky/redis.zig
[memcached.zig]: https://github.com/lalinsky/memcached.zig
[http.zig]: https://github.com/karlseguin/http.zig
[llhttp]: https://llhttp.org/
