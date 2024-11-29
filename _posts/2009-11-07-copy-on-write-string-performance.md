---
comments: true
date: 2009-11-07 07:35:36
layout: post
slug: copy-on-write-string-performance
title: Copy-on-write string performance
wordpress_id: 173
categories:
- programming
tags:
- copy-on-write
- hacks
- performance
- qstring
- stl
- string
- wstring
---

Since I've started using Qt, I loved the "implicit sharing" concept it uses for it's strings and container types. It become so much easier to pass these data around. I wasn't aware that some STL implementations have copy-on-write semantics for strings as well. When I saw some recommendations for `std::string` on Stack Overflow, I've decided to check out the implementation in GCC and discovered that it indeed does some reference counting.

So the next step was comparing the implementations. I wrote a little [program](/tmp/string-bench.cpp) today to check how `QString` and `std::wstring` compare in terms of copy-on-write performance. Since `QChar` is 2 bytes and `wchar_t` is 4 bytes on my machine, it wouldn't be completely fair comparison, so I've included also `std::string`. The results were quite surprising for me. STL does almost always better, but for some reason I wasn't able to make not dereference the string on read-only operations.








wchar_t*
QString
std::wstring
std::string




Read

0 ms


0 ms


2143 ms


2224 ms





Write

0 ms


5588 ms


2621 ms


2570 ms





Copy

1618 ms


601 ms


116 ms


117 ms





Copy + read

-


601 ms


6161 ms


5079 ms





Copy + write

-


11036 ms


6822 ms


6843 ms





Copy + append

-


5801 ms


4650 ms


3482 ms



The table shows times in milliseconds for 10000000 repeated operations on a 200-character long string.

  * "**Read**" just reads the string one character at a time, using the default `[]` operator. I've tried hard to find a cheaper way to do this for STL strings, but I failed (I wasn't interested in using `s.data()` and then working with the primitive array, I wanted to work with the object directly).
  * "**Write**" writes to all characters of the string, again one character at a time.
  * "**Copy**" assigns one string to another, using the default `=` operator for string classes and `memcpy` for `wchar_t*`.
  * "**Copy + read**" is the same as "**Copy**", followed by "**Read**" performed on the copy.
  * "**Copy + write**" is the same as "**Copy**", followed by "**Write**" performed on the copy.
  * "**Copy + append**" is again the same as "**Copy**", followed by appending a short string to the copy.

I guess I should note that this wasn't meant to be a generic benchmark of the string classes. I just wanted to know performance details about the copy-on-write implementations in them. The conclusion for me is that the STL strings in GCC are better than I always thought, but the fact that they dereference the data on read-only operations is not very nice.
