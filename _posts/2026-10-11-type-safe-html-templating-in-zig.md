---
layout: post
title: Type-safe HTML templating in Zig
categories:
- programming
tags:
- zig
- networking
- web
- html
- templating
---

I've done a lot of web development over the years. I started around the year 2000, just after PHP 3.0 was released and started taking over the web world. I've used all kinds of approaches for generating HTML: raw PHP, string concatenation in Python, Smarty templates in PHP, Template Toolkit in Perl, and Jinja/Django templates in Python. Then, as the world moved on to SPAs and JavaScript frameworks, I stopped working on frontends.

Two years ago, I picked up Zig, and I'd never have imagined I'd be considering web development in such a low-level language. But as I was building my own ecosystem with [zio](https://github.com/lalinsky/zio) and [Dusty](https://github.com/lalinsky/dusty), I realized it's not that far-fetched. I built these libraries for API backends, where the output is JSON or MessagePack, but then I saw that with proper use of arena allocators, even web development is actually manageable, since you don't need to remember to deallocate all the strings that are typically involved. And with Zig's comptime, you can build very high-level interfaces without losing the performance benefits. And with the help of [htmx](https://htmx.org/) or [Datastar](https://data-star.dev/), you can even build SPAs without touching JavaScript/TypeScript, which I promised myself I'd never do.

That said, one thing was still missing: templating. String concatenation was not an option, I didn't want to go back to the pre-2000 CGI days. I had a few requirements for a templating library:

1) It has to be type-safe, with all inputs typed and checked at compile time. I've dealt with dynamic languages enough in my life, and I don't want to deal with runtime template errors anymore.
2) It has to be fast. No allocations, no intermediate state, just write the output to `std.Io.Writer` directly.
3) It needs to have safe defaults. I don't want to deal with XSS anymore, so all inputs have to be escaped by default.
4) It should still feel like writing HTML. I didn't want a Zig-based DSL I'd have to fight constantly.

I couldn't find any solution that would satisfy all these requirements. Then I saw [Ziex](https://ziex.dev/) and I was amazed. It never occurred to me that the [JSX](https://legacy.reactjs.org/docs/introducing-jsx.html) approach could be done outside of JavaScript. The developer experience Nurul Huda managed to build in Zig was just unimaginable to me. It didn't fit my needs directly, because I didn't want a WASM framework, but the syntax and his approach to integrating Zig code into templates gave me some ideas. Then, after further research, I discovered [Templ](https://templ.guide/) for Go, and it was pretty obvious to me what I wanted to build.

So, let me present [zt](https://github.com/lalinsky/zt) *(I'm not good at naming, sorry)*. It's an adaptation of Templ to Zig. The templates are parsed and transpiled to Zig code during `zig build`. That means you can import them directly from your app code, but also, perhaps even more importantly, the templates can import your app code. As a result, all parameters can be fully typed. If something can't be rendered, the Zig compiler will tell you so. The generated code is fairly simple, all it does is write to a `std.Io.Writer`. You can write directly to a socket or accumulate the output in a buffer, your choice. Here is what a template looks like:

```zig
const User = @import("../models.zig").User;

pub templ Layout(title: []const u8) {
    <!DOCTYPE html>
    <html>
        <head>
            <title>{title}</title>
        </head>
        <body>
            @children
        </body>
    </html>
}

pub templ HomePage(user: ?User) {
    @Layout("Home") {
        if (user) |u| {
            <h1>Welcome back, {u.name}!</h1>
            <a href="/users/{u.id}">Your profile</a>
        } else {
            <h1>Welcome, guest!</h1>
            <a href="/login">Log in</a>
        }
    }
}
```

And here is how you would use it from Zig code:

```zig
const std = @import("std");
const User = @import("models.zig").User;
const pages = @import("templates/pages.zig");

pub fn main(init: std.process.Init) !void {
    var buf: [4096]u8 = undefined;
    var stdout = std.Io.File.stdout().writer(init.io, &buf);
    const w = &stdout.interface;

    const user: User = .{ .id = 1, .name = "Lukas" };
    try pages.HomePage.render(.{user}, w);
    try w.flush();
}
```

If you want to use it inside Dusty to render an HTML page, you just do this:

```zig
try res.render(.html, pages.HomePage, .{user});
```

*As a side note, the `main` snippet above has a very non-obvious bug in error handling if you use the same pattern somewhere `std.Io` cancellation matters. I'll probably write a blog post about it, but if you are interested, check out this [Ziggit thread](https://ziggit.dev/t/making-it-harder-to-swallow-error-canceled/16556). The `render` function in Dusty that wraps the `std.Io.Writer` handling solves it.*

All dynamic values are HTML-escaped by default, both in text and in attributes, and values in URL attributes like `href` or `src` are also checked for dangerous schemes. If you really need to output raw HTML, you have to ask for it explicitly with `{!value}`, so it's easy to spot in the template. The parser also requires all elements to be properly closed, so you can't accidentally produce broken markup. It won't protect you from everything, but it removes the most common sources of XSS.

What I like the most is that template errors are just Zig compiler errors. If you make a typo in the template above and write `{u.nmae}` instead of `{u.name}`, `zig build` will tell you:

```
src/templates/pages.zig:45:43: error: no field named 'nmae' in struct 'models.User'
            try zt.writeEscaped(writer, u.nmae); // pages.zt:18:31
                                          ^~~~
src/models.zig:1:18: note: struct declared here
pub const User = struct {
                 ^~~~~~
```

The error points to the generated code, but each line has a comment with the location in the original template. The same applies to the calling side, if you try to pass a string where `HomePage` expects a `?User`, it won't compile.

Beyond the basics shown here, you can build reusable components and pass them around as values, and with `@children` you can build layouts that wrap other templates, which covers what you'd use template inheritance for in other languages. Control flow is just Zig, so `if`, `for` and `switch` work as you'd expect, including payload captures. All of it still compiles down to a sequence of writes, without any allocations.

You can have a look at the [TODO](https://github.com/lalinsky/dusty/tree/main/examples/todo) example in Dusty. It's a simple htmx-based web app that showcases this library, plus other parts of Dusty that are needed for building a dynamic SPA.

One part of the syntax is still open. Block-level control flow is written as plain Zig, following the example of Templ, so a line starting with `if`, `for` or `switch` is parsed as code. That means text like "if you forgot your password..." on its own line is a parse error, and you have to wrap it in an element or write it as a string expression. I'm considering switching to `@if`, `@for` and `@switch`, similar to Razor or Twirl, which would remove the ambiguity, at the cost of slightly noisier templates. If you have an opinion on this, please leave a comment in [this GitHub issue](https://github.com/lalinsky/zt/issues/27).

Although multiple people are already using it, the project is not very mature yet, so I welcome any feedback. You can open a GitHub issue if you find any problems, or ping me on the Zig Discord.