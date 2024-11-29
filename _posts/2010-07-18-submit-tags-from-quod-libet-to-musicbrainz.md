---
comments: true
date: 2010-07-18 09:58:15
layout: post
slug: submit-tags-from-quod-libet-to-musicbrainz
title: Submit tags from Quod Libet to MusicBrainz
wordpress_id: 514
categories:
- musicbrainz
- tools
tags:
- musicbrainz
- quod libet
---

I wanted to write something like this for a long time, but for some reason never did it. MusicBrainz has support for folksonomy tagging since 2007, but the coverage of track tags is still not very good. I try to keep some tags in the "genre" tag in audio files, but even with one-time import tool, I'm sure I'd not remember to run this on new files. So the idea here is to submit these tags to MusicBrainz as I listen to the files in my music player ([Quod Libet](http://code.google.com/p/quodlibet/)). It's inspired by a Quod Libet plugin called [LastFMTagger](http://code.google.com/p/thisfred-quodlibet-plugins/), which does something similar, but for Last.fm. I had some free time today, so I wrote a [plugin](http://dl.dropbox.com/u/5215054/mbtagsubmit.py) that does one-way synchronization of tags from Quod Libet to MusicBrainz. You can install the plugin using the following commands:

    mkdir -p ~/.quodlibet/plugins/events/
    cd ~/.quodlibet/plugins/events/
    wget http://dl.dropbox.com/u/5215054/mbtagsubmit.py

After you enable it and let it know your MusicBrainz username and password (Music → Plugins), it will watch the songs you listen and if any of them has a "musicbrainz_trackid" and at least one "genre" tag, it will use the MusicBrainz web service to submit them. The submission normally happens only every half an hour, but if you change many files in a short time, it will submit them in batches of 20 tracks (the maximum number allowed by the web service) every 2 minutes.

