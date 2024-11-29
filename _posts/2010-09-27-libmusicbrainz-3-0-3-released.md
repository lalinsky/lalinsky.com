---
comments: true
date: 2010-09-27 14:14:17
layout: post
slug: libmusicbrainz-3-0-3-released
title: libmusicbrainz 3.0.3 released
wordpress_id: 674
categories:
- musicbrainz
tags:
- release
- libmusicbrainz
- musicbrainz
---

I've just released libmusicbrainz 3.0.3. Most of the code was done a long time ago, but I never convinced myself to release it. It would have probably stayed that way, if it wasn't for [Yury G. Kudryashov's patches](http://thread.gmane.org/gmane.comp.audio.musicbrainz.devel/4031) (thank you!).

  * [Source code](http://ftp.musicbrainz.org/pub/musicbrainz/libmusicbrainz-3.0.3.tar.gz) (104K)
  * [API documentation](http://users.musicbrainz.org/~luks/docs/libmusicbrainz3/)

The following things were changed in this version:

  * Use `Requires.private` instead of `Requires` for dependencies in `libmusicbrainz3.pc`
  * Support for reading and submitting ISRCs
  * Support for reading ratings for all entity type
  * Full release group and label support
  * C wrappers for `extractFragment` and `extractUuid`
  * Support for filtering by any Lucene query

