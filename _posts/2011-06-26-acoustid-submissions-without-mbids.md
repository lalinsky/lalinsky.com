---
comments: true
date: 2011-06-26 21:27:36
layout: post
slug: acoustid-submissions-without-mbids
title: Acoustid submissions without MBIDs
wordpress_id: 1476
categories:
- acoustid
tags:
- acoustid
- fingerprinter
- release
---

I've released a new version of the Acoustid server that doesn't require submissions to include MusicBrainz recording IDs. Applications can send textual metadata (track, artist, album, album artist, year, track number, disc number) instead and the server will try to match the tags to MusicBrainz by itself. The matching is actually not yet implemented, but the server is able to collect the data. The format of the metadata is described in the [web service documentation](http://acoustid.org/webservice).

I've also released a new version of the [Acoustid fingerprinter](http://acoustid.org/fingerprinter), which can utilize this new functionality and if your files don't have MBIDs embedded in tags, it will send the metadata it can find in tags, instead of completely skipping the files. You can download the new version here:

  * [Windows Application](https://github.com/downloads/lalinsky/acoustid-fingerprinter/acoustid-fingerprinter-0.2-win32.zip)
  * [Mac OS X Application](https://github.com/downloads/lalinsky/acoustid-fingerprinter/acoustid-fingerprinter-0.2-mac.dmg)
  * [Source Code Tarball](https://github.com/downloads/lalinsky/acoustid-fingerprinter/acoustid-fingerprinter-0.2.tar.gz)

Note that this is the first time I actually built the fingerprinter on OS X, so if the package doesn't work for you, please let me know. It should work on Intel Macs with OS X 10.4 or never.
