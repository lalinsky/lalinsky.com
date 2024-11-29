---
comments: true
date: 2010-12-16 20:15:24
layout: post
slug: acoustid-moved-to-a-new-server
title: Acoustid moved to a new server
wordpress_id: 940
categories:
- acoustid
tags:
- acoustid
- chromaprint
- fingerprinting
---

Since I announced the Acoustid project, I got over 1.1 million fingerprint submissions (mostly from [MusicBrainz editors](http://acoustid.org/stats)), covering about 580 thousand unique MusicBrainz track IDs. In the background I was running a process that imported the raw submissions and merged similar tracks. All this was done on a virtual machine with 1GB of RAM. It wasn't very fast, but I was surprised it was even able to handle such amount of data. At some point I also enabled the lookup service, but I didn't want to announce it, because it took too long to do fingerprint searches on the server.

In the past few days I've been setting up a new server from [Hetzner](http://www.hetzner.de/en/). It's still not powerful enough for hosting the "final" service, but it should be fine to allow testing of the web service. With the new hosting, I'm also able to make the data dumps publicly available for download. It will take me some time to have an easy way to import/export them, but exporting a simple CSV file with all the submissions I got should be simple enough.

Finally, I hacked up [some scripts in Python](https://code.launchpad.net/~luks/+junk/acoustid-tools) to demonstrate how to use libchromaprint and Acoustid to do audio file lookups. For now it requires the latest version of [libchromaprint from bzr](https://code.launchpad.net/~luks/chromaprint/trunk), but I'm planning to make a new release soon.

I must say that I'm really happy about the project. It started as a very simple experiment, but it's becoming more and more realistic that it can end up being useful. I was also very surprised how much interest is there. I know about some people who are experimenting with chromaprint, I've been contacted by a few small companies that are interested in the project (way too early for that!), somebody even added it to [Wikipedia](http://en.wikipedia.org/wiki/Acoustic_fingerprint) next to the big commercial fingerprinting systems. :)
