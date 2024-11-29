---
comments: true
date: 2010-10-30 19:39:57
layout: post
slug: acoustid-fingerprinter-0-1-released
title: Acoustid Fingerprinter 0.1 released
wordpress_id: 867
categories:
- acoustid
tags:
- acoustid
- chromaprint
- fingerprinter
- release
---

After posting about the beta version of Acoustid Fingerprinter, some people successfully used it to submit fingerprints and I also started using it as the main tool for submitting fingerprints, so I think it's time for a proper release. More details on the wiki:

  * [Acoutid Fingerprinter 0.1](http://wiki.acoustid.org/wiki/Fingerprinter)
  * [Chromaprint 0.1](http://wiki.acoustid.org/wiki/Chromaprint)

If you are using Windows, you can simply download and unzip the binary package. On Ubuntu (Lucid, Karmic and Maverick), you can install it from my PPA:

    sudo apt-add-repository ppa:luks/acoustid
    sudo apt-get update
    sudo apt-get install acoustid-fingerprinter

I've tested only the Maverick build, if there are problems with the other ones, please let me know. For other platforms, you will have to build from the source code, but I think it's not that hard. Instructions are in README files in the tarballs.

If you have MusicBrainz-tagged music collection, please download the fingerprinter and help me collect as many fingerprints as possible. Thank you! (And thanks to [people who already submitted ~800k fingerprints](http://acoustid.org/stats) using not-so-nice tools.)

