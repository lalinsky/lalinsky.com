---
comments: true
date: 2011-08-06 20:21:12
layout: post
slug: acoustid-fingerprinter-0-4-released
title: Acoustid fingerprinter 0.4 released
wordpress_id: 1541
categories:
- acoustid
tags:
- acoustid
- fingerprinter
- release
---

I've just released a new version of the Acoustid fingerprinter. I've sneakily released 0.3 some time ago, but it was available only on Linux. The only change was support for reading of the PUID tags. This release brings that functionality also to Windows and OS X, plus a few bug fixes.

Changes since the last version are:

  * New icon: ![](https://github.com/lalinsky/acoustid-fingerprinter/raw/master/images/acoustid-fp-16.png)
  * HTTP proxy support using the `$http_proxy` environment variable
  * Support for reading PUIDs from tags
  * Fixed submission of the file format

You can download the new version on the [Acoustid website](http://acoustid.org/fingerprinter).

