---
layout: post
title: Timeouts in zio
categories:
- programming
tags:
- zig
- networking
- async
---

The first versions of [zio](https://github.com/lalinsky/zio) had no timeout support. It was not an oversight, it was a design problem that I
didn't know the solution for, at that time. The issue is that zio networking was always meant to be
primarily used through the standard reader/writer interfaces. These interfaces have no concept of a timeout.
So how to thread that in?

Well, one option is to have a custom reader that has a `setTimeout` method, and the supplied interface
can be unaware of that. That works, but it's limited to just network reads/writes.


What you really want is something like [`context.Context`](https://pkg.go.dev/context) in Go, where you can set a deadline on the context,
and then the context is passed everywhere. Then any operation could be interrupted when the
deadline expires, doesn't matter if it's a channel receive, or sleep, or anything else.
But that has the same problem, all the libraries would need to be aware of this context and respect it.

I wanted a solution that can be used with existing libraries. Then I remembered that I used
Python with Trio in the past, and that has a context manager called [`trio.move_on_after`](https://trio.readthedocs.io/en/stable/reference-core.html#trio.move_on_after).
It turns out it also exists in Python's asyncio as [`asyncio.timeout`](https://docs.python.org/3/library/asyncio-task.html#asyncio.timeout). This is how you use it:

```python
    async with asyncio.timeout(10):
        await long_running_task()
```

All the code inside the block doesn't need to be aware of timeouts.
It will simply get canceled when the timeout triggers. And because cancellation is an integral part of asyncio,
all async code in Python can handle that already.

That was the A-HA moment. I could do the same in zio. I just need a tiny bit of support in the scheduler.
I can register a timer on the event loop, and when it triggers, I simply cancel the task.

The important part is that this uses the exact same cancellation mechanism that's already there for
explicit cancellation. There is nothing special about timeouts. When the timer fires, whatever blocking
operation the task is currently running gets interrupted and returns `error.Canceled`. That error then
bubbles up through the call stack just like any other error, running all the `defer` and `errdefer`
cleanup along the way, until someone handles it. None of the code in between needs to know that a timeout
was involved, or even that cancellation is a thing. It just sees a normal error from a normal function call.

The only addition on top of plain cancellation is that we keep track of whether the cancellation came from
the user or from a timeout. That's what lets us turn it back into a timeout-specific error at the boundary.
It's not essential, but nice to have.

This is exposed as [`zio.AutoCancel`](https://lalinsky.github.io/zio/apidocs/#zio.AutoCancel), so I can do something like this:

```zig
var timeout: zio.AutoCancel = .init;
defer timeout.clear();

timeout.set(.fromSeconds(60));

handleRequest(...) catch |err| switch (err) {
    error.Canceled => |e| return if (timeout.check(e)) error.RequestTimeout else e,
    else => |e| return e,
};
```

The API is small. `set` arms the timer: it registers a one-shot timer on the event loop that will cancel
the current task after the given duration. `clear` disarms it again, which is why it lives in a `defer`.
When the block exits, whether normally or with an error, we want the timer removed from the event loop so
it can't fire later. And `check` tells you whether this timeout was the one that fired, so you can convert
it into a timeout error instead of propagating the plain `error.Canceled`.

You can also nest these timeouts. For example, in the code above, `handleRequest` could have another
`zio.AutoCancel` inside with a shorter deadline. Both timers are armed, and whichever fires first cancels
the task. There's no coordination between them.

This is what `check` is for. The `error.Canceled` bubbles up through every level, and each `AutoCancel`
asks whether it was its own timer that fired. The one that fired claims the error and converts it; the
others return false and let it keep propagating. So each timeout gets attributed to exactly the level that
set it, no matter how deep the nesting goes.

And if this is a standalone task, you maybe don't need the conversion to a timeout error at all. There's
nobody up the stack to tell apart a timeout from any other cancellation, so you can skip `check` entirely
and just let the `error.Canceled` propagate out of the task:


```zig
var timeout: zio.AutoCancel = .init;
defer timeout.clear();

timeout.set(.fromSeconds(60));

try handleRequest(...);
```


Of course, none of this rules out the more targeted approach. When all you need is a timeout on a single
operation, like `accept` or `connect`, or on a reader/writer, you can still set one directly on that
operation; zio supports that mode as well. The `AutoCancel` block is just the general tool that covers
everything else, including code that knows nothing about timeouts.

I'm really happy with this design and it's one of the parts of zio's API I'm proud of. It's very easy to use and it has very little cost on the runtime.
