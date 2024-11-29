---
comments: true
date: 2011-03-11 19:02:38
layout: post
slug: taglib-1-7-released
title: TagLib 1.7 released
wordpress_id: 1250
categories:
- taglib
tags:
- git
- github
- release
- taglib
---

TagLib 1.7 has been released. This release adds support for Monkey’s Audio files and cover art reading/writing for WMA and FLAC files. There is also a number of bug fixes, more details in the changelog. There are two important changes associated with this release:

  1. **Removed autoconf/automake support** -- If you used `./configure && make` to build TagLib, you will have to switch to the CMake-based build system. The [INSTALL](https://github.com/taglib/taglib/blob/master/INSTALL) file has been updated with instruction on how to build TagLib using CMake.
  2. **Source code moved to GitHub** -- TagLib's source code is now hosted in a Git repository on [GitHub](https://github.com/taglib/taglib). The KDE SVN repository will stay available for some time, to not break anything, as the decision about the migration has been done very quickly, but it will not get any updates and will get removed in near future.

Download:

  * [Source Code Tarball](http://developer.kde.org/~wheeler/files/src/taglib-1.7.tar.gz) (or [here](https://github.com/downloads/taglib/taglib/taglib-1.7.tar.gz)) (522K)

Full changelog since version 1.6:

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
  * Fixed bitrate calculation for WAV files.

Changes since the 1.7 RC can be found in the [NEWS](https://github.com/taglib/taglib/blob/master/NEWS) file.
