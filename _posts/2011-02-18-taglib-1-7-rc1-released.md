---
comments: true
date: 2011-02-18 20:03:09
layout: post
slug: taglib-1-7-rc1-released
title: TagLib 1.7 RC1 released
wordpress_id: 1227
categories:
- taglib
tags:
- release
- taglib
---

Even though I wasn't planning to make a 1.7 release by the time 1.6.3 was released, moving forward with the 1.x series seems to be the easiest way to go. There are many new features in this version. The biggest ones are full support for Monkey's Audio files and cover art support for WMA and FLAC files. There are also a number of bug fixes. The reason why I decided to go with a RC is that some code had to be rewritten in order to add the new features, so I'd like more people to test it before there is a final release. Also, if I've forgotten about somebody's patch that should be included in the final release, please let me know.

One important thing is that TagLib 1.7 no longer supports building using automake/autoconf. If you used this build system, try our CMake-based build system which was available in TagLib for a long time. The INSTALL document has been rewritten to cover the most important CMake options.

Download:

  * [taglib-1.7rc1.tar.gz](http://launchpad.net/taglib/trunk/1.7rc1/+download/taglib-1.7rc1.tar.gz) (530K)

Full changelog for this release:

  * Support for reading/writing tags from Monkey's Audio files. ([BUG:210404](https://bugs.kde.org/show_bug.cgi?id=210404))
  * Support for reading/writing embedded pictures from WMA files.
  * Support for reading/writing embedded pictures from FLAC files ([BUG:218696](https://bugs.kde.org/show_bug.cgi?id=218696)).
  * Implemented `APE::Tag::isEmpty()` to check for all APE tags, not just the basic ones.
  * Added reading of WAV audio length. ([BUG:116033](https://bugs.kde.org/show_bug.cgi?id=116033))
  * Exposed FLAC MD5 signature of the uncompressed audio stream via `FLAC::Properties::signature()`. ([BUG:160172](https://bugs.kde.org/show_bug.cgi?id=160172))
  * Added function `ByteVector::toHex()` for hex-encoding of byte vectors.
  * WavPack reader now tries to get the audio length by finding the final block, if the header doesn't have the information. ([BUG:258016](https://bugs.kde.org/show_bug.cgi?id=258016))
  * Fixed a memory leak in the ID3v2.2 PIC frame parser. ([BUG:257007](https://bugs.kde.org/show_bug.cgi?id=257007))
  * Fixed writing of RIFF files with even chunk sizes. ([BUG:243954](https://bugs.kde.org/show_bug.cgi?id=243954))
  * Fixed compilation on MSVC 2010.
  * Removed support for building using autoconf/automake.
  * API docs can be now built using "make docs".

