---
comments: true
date: 2011-03-06 14:26:07
layout: post
slug: picard-0-13-released
title: Picard 0.13 released
wordpress_id: 1232
categories:
- musicbrainz
tags:
- musicbrainz
- picard
- release
---

MusicBrainz Picard [wasn't under active development](http://forums.musicbrainz.org/viewtopic.php?id=2540) for a long time. In fact, the last release was in November 2009. Even due to the situation, some bug fixes accumulated in the source control repository over the time, so the primary goal of this release is to publish these bug fixes. There were also a few larger patches, such us the [sorting support by Aaron Lambers](http://article.gmane.org/gmane.comp.audio.musicbrainz.devel/4105), but these are not included in this release. I'm hoping that patches like that will get committed now that 0.13 is released and a new release will follow in less than a year from now. :)

Download:

  * [Windows Installer](http://ftp.musicbrainz.org/pub/musicbrainz/picard/picard-setup-0.13.exe) (9.0M)
  * [Source Code Tarball](http://ftp.musicbrainz.org/pub/musicbrainz/picard/picard-0.13.tar.gz) (1.4M)


Changes since the last release:

  * Changed Picard icon license to CC by-sa.
  * Small UI consistency changes.
  * Albums with tracks linked to more than one file are never marked as "completed".
  * Fixed matching of scanned files to tracks while the album is still loading.
  * Support for properly embedded FLAC pictures.
  * Existing embedded images in APE and ASF files are removed only if there are new images to replace them.
  * More strict tagger script validation.
  * Fixed the $truncate tagger script function.
  * Proper rounding of track durations.
  * Fixed a bug with saving images larger than 64k to WMA files.
  * Added a $swapprefix tagger script function.
  * Release events with a date are preferred over the ones without a date.
  * Files that are being saved as marked as pending.
  * Updated .desktop file to allow opening Picard with multiple files.
  * Handle the "open file" event on Mac OS X.
  * Added timeouts to the HTTP web service client.
  * Fixed a bug with albums missing the expand/collapse icons.

