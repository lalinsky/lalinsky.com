---
comments: true
date: 2011-01-02 21:11:54
layout: post
slug: chromaprint-plug-in-for-gstreamer
title: Chromaprint plug-in for GStreamer
wordpress_id: 972
categories:
- acoustid
tags:
- acoustid
- c++
- chromaprint
- gstreamer
- python
---

It's been a long time since I learned some new framework (or even wrote some real code), so I decided to write a [GStreamer](http://www.gstreamer.net/) plug-in that will wrap [libchromaprint](http://wiki.acoustid.org/wiki/Chromaprint) and make it very easy to generate Chromaprint fingerprints in GStreamer applications. This was inspired by a similar plug-in that [Milosz Derezynski](https://github.com/mderezynski) wrote for [MusicDNS/libofa](http://code.google.com/p/musicip-libofa/) (the plug-in is now integrated in the official GStreamer distribution). Using the code from `gst-ofa` and `gst-template`, it turned out to be pretty easy. After a couple of hours, I was able to run commands like this and get valid fingerprints:


    
    
    $ gst-launch-0.10 -t filesrc location=/path/to/audio/file ! decodebin ! audioconvert ! chromaprint duration=60 ! fakesink sync=0 | grep 'chromaprint fingerprint'
    



I also wrote a simple Python script that uses the GStreamer element to generate fingeprints and look them up using the Acoustid web service. It's nice to see how little code is necessary to have a fully working fingerprinting application.

The only disadvantage is that it's too slow. :) I had some GStreamer code in Picard for decoding audio files, but I eventually removed it because it was significantly slower compared to the other decoding options (FFmpeg and DirectX). It seems that the situation is still the same, because even compared to calling the `ffmpeg` binary in a subprocess (which I used in `[acoustid-tools](http://bazaar.launchpad.net/~luks/%2Bjunk/acoustid-tools/annotate/head:/audiodecoder.py)`), GStreamer is about three times slower. It's not too bad for applications that rarely use the functionality, but I wouldn't use it in a tagger that needs to identify many files as fast as possible.

Anyway, the project is on [Launchpad](https://launchpad.net/gst-chromaprint), where you can download the [source code using bzr](https://code.launchpad.net/~luks/gst-chromaprint/trunk) or a [tarball](http://launchpad.net/gst-chromaprint/trunk/0.1/+download/gst-chromaprint-0.1.0.tar.gz). It depends on the development version of libchromaprint.

