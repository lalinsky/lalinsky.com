---
layout: post
title: Better synchronization primitives for Zig's `std.Io`
categories:
- programming
tags:
- zig
- async
- concurrency
---

Zig's `std.Io` interface, introduced in version 0.16, comes with a number of synchronization primitives,
from low-level ones like `std.Io.Mutex` and `std.Io.Condition` to higher-level ones like `std.Io.Queue`.
These are all implemented on top of the `futexWait`/`futexWake` functions in the `std.Io` vtable. There are three problems with them:

1) They do not work across multiple `std.Io` instances. It might seem like a made up problem, but in server applications, it's common to have two layers,
   one for the networking, and one for computation. In the networking layer, you benefit from async I/O and event loop integration. However, you can't
   block the event loop by expensive computation, so you offload it to a thread pool. In terms of `std.Io`, that means two instances,
   one `std.Io.Evented` (once implemented) or `zio.Runtime` (ready today), and one `std.Io.Threaded`. And then you have the problem that these
   two need to be able to communicate.

2) They have race conditions. When a cancellation races with a signal, `std.Io.Condition` can miss the cancellation, or worse,
   deadlock ([#36139](https://codeberg.org/ziglang/zig/issues/36139)). And because `std.Io.Condition` is the foundation for most
   of the other primitives, they are all affected. On top of that, `std.Io.RwLock` has two bugs of its own that can let a writer
   enter the critical section while readers still hold the lock, one when a cancellation races with an unlock
   ([#36178](https://codeberg.org/ziglang/zig/issues/36178)) and one in `tryLock` ([#36217](https://codeberg.org/ziglang/zig/issues/36217)).

3) They do not support timeouts in many places. While a timeout has been added to `std.Io.Condition` in Zig master, which should be hopefully
   released soon as Zig 0.17, it's very common to also need timeouts in `std.Io.Queue` operations. Unbounded waiting in server applications
   can be problematic.

Triggered by a [thread on Ziggit](https://ziggit.dev/t/threaded-and-evented-coexistence/16652), I decided to implement [xsync.zig](https://github.com/lalinsky/xsync.zig), a new set of
synchronization primitives that work across `std.Io` instances, since I had a proven design for this in `zio`. It was easy to adapt
it to `std.Io` futexes, but I hit some performance issues and I really wanted these to be as fast as possible,
comparable to the original ones. So I tried a few other approaches and eventually settled on one design that works pretty fast
when used with 1-2 instances, and degrades to a slower version when used with more than that. The core idea is that each primitive
remembers which `Io` instance every waiter parked through, and wakes it back through that same one. These new APIs cover `Mutex` and `Condition`;
the other ones are built on top of them, so they could be copied over from the Zig standard library (with bug fixes applied).

I also took the opportunity to rewrite `Queue` to use futexes directly, instead of `Mutex`/`Condition`. I ended up with a version that is
faster than the one in the Zig standard library, and also supports multiple io instances. And as a bonus, I added support for timeouts.

You can find code at [GitHub](https://github.com/lalinsky/xsync.zig), licensed the same as Zig itself, and if you have any comments or questions, ask me in this [Ziggit thread](https://ziggit.dev/t/xsync-zig-synchronization-primitives-that-work-across-std-io-instances/16708).
