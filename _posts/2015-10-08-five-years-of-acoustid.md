---
layout: post
title: Five years of AcoustID
categories:
- acoustid
tags:
- acoustid
- chromaprint
---

It's hard to tell the exact date when the [AcoustID](https://acoustid.org/) project started, but if we go by the
first entry in the database, it was October 8, 2010. That means project turned five this week!
I thought it's a good opportunity to gather some statistics from those five years.

Back in 2010, we were starting from scratch. We had an empty database, while the solution that AcoustID was
replacing ([MusicDNS/PUID](https://wiki.musicbrainz.org/Fingerprinting#PUID)) had fingerprints for 4.4 million
MusicBrainz recordings (34% of all MusicBrainz recordings at that time).
It took about two years to catch up with that number.
Today, AcoustID can identify 8.3 million MusicBrainz recordings, which is 54% of all recordings in the MusicBrainz
database. So about twice the size and the fingerprint database is growing faster than MusicBrainz itself, which means
eventually it might be able to identify the most of MusicBrainz recordings.

Since early 2011, we also started accepting fingerprints without links to the MusicBrainz database and
the number of those has grown even faster, so only a small part of the AcoustID fingerprint database
is actually linked to MusicBrainz now. The total number of unique fingerprints ("AcoustIDs") in the database is currently 25.5 million.

Here you can see the numbers on a timeline:

<iframe width="600" height="371" seamless frameborder="0" scrolling="no" src="https://docs.google.com/spreadsheets/d/1EsL3v8k5F4DO8yhiiC-uWFTRF_xqTLU5lSbhY7HSI28/pubchart?oid=532574002&amp;format=interactive"></iframe>

Traffic has naturally grown during the five years as well, but similarly to the database size, the growth is mostly linear.
This because of the focus on full audio file tagging and integration with MusicBrainz, which means AcoustID only ends up being used in specialized applications.

<iframe width="600" height="371" seamless frameborder="0" scrolling="no" src="https://docs.google.com/spreadsheets/d/1EsL3v8k5F4DO8yhiiC-uWFTRF_xqTLU5lSbhY7HSI28/pubchart?oid=1788537123&amp;format=interactive"></iframe>

*Unfortunatelly, the first version released 2010 was pretty minimalistic and did not include request statistics, so we only have these numbers starting from August, 2011.*

[MusicBrainz Picard](https://picard.musicbrainz.org/) is the biggest source of users, which is not surprising, because AcoustID has been created for MusicBrainz Picard.
But there are other free applications that use AcoustID -- [beets](http://beets.radbox.org/), [MusicBee](http://getmusicbee.com/), [FileBot](http://www.filebot.net/),
[VLC](http://www.videolan.org/), [Clementine](https://www.clementine-player.org/), [puddletag](http://puddletag.sourceforge.net/)
[Kid3](http://kid3.sourceforge.net/), [Quod Libet](https://quodlibet.readthedocs.org/en/latest/) and many many other smaller applications.
There are also a few commercial applications that use AcoustID. The number of applications using the service every month is now above 100 and still growing.

<iframe width="600" height="371" seamless frameborder="0" scrolling="no" src="https://docs.google.com/spreadsheets/d/1EsL3v8k5F4DO8yhiiC-uWFTRF_xqTLU5lSbhY7HSI28/pubchart?oid=210337696&amp;format=interactive"></iframe>

It's quite easy to use AcoustID from about any programming language now.
Chromaprint fingerprints can be generated from [Python](https://github.com/sampsyo/pyacoustid), [Ruby](https://github.com/TMXCredit/chromaprint), [Rust](https://github.com/jameshurst/rust-chromaprint), [Go](https://github.com/go-fingerprint/gochroma), [JavaScript](https://github.com/parshap/node-fpcalc) and I'm probably missing a few.
There are wrappers for C# and Java, but those are always developed directly inside the apps that use them.
There is direct support for generating Chromaprint fingerprints in [GStreamer](http://cgit.freedesktop.org/gstreamer/gst-plugins-bad/tree/ext/chromaprint/gstchromaprint.c) and recently also [FFmpeg](https://www.ffmpeg.org/ffmpeg-formats.html#chromaprint-1).
And there are also alternative implementations of the Chromaprint algorithm in C# ([1](https://github.com/wo80/AcoustID.NET), [2](https://github.com/zsoltsajben/NChromaprint)) and [JavaScript](https://github.com/bjjb/chromaprint.js).

I have not been working on AcoustID very actively lately and I know that there are some things that need to be done,
but I'm still happy that the project is able to run pretty much on its own with very little support, that the architecture
designed five years ago is still capable of handling today's traffic and I'm not worried that it won't be able to
handle the traffic five years from now.

Happy birthday, AcoustID!
