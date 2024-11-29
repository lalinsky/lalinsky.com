---
comments: true
date: 2010-07-24 18:36:57
layout: post
slug: introducing-chromaprint
title: Introducing Chromaprint
wordpress_id: 529
categories:
- acoustid
tags:
- acoustid
- audio
- c++
- chromaprint
- fingerprinting
- musicbrainz
---

After several months of reading research papers, learning and weekend coding, I'm very happy to make the half-finished code of my audio fingerprinting library public. :) I'm doing this mostly for selfish reasons, because it will force me to stop thinking in "hacker mode" and hopefully properly finish it, and I also hope to get some help and feedback from other people. There is nothing for regular users yet though, just for developers or people not afraid of the command line.

It all started in February this year, when when I got my Google Alerts mail for "musicbrainz picard" which included a link to [this paper](http://portal.acm.org/citation.cfm?id=1399913&CFID=98145194&CFTOKEN=53175875) ("_Waveprint: Efficient wavelet-based audio fingerprinting_" (2007) by Shumeet Baluja and Michele Covell). I've never paid much attention to how audio identification systems are actually implemented, but I found it interesting that a paper published by Google researchers cited [Picard](http://musicbrainz.org/doc/PicardTagger), especially because Picard doesn't implement any fingerprinting algorithm, just uses [libofa](http://code.google.com/p/musicip-libofa/). Anyway, I've read the paper and realized that maybe it's not that hard to implement such a system. No tough DSP stuff or scary mathematics (of course there is some DSP stuff and mathematics, but mostly basics). The system described in the paper seemed quite straight-forward to implement, so I become curious and decided to give it a try. Later on I realized that it's perhaps not the best system and that even the authors published new papers describing different approaches. That, combined with the fact that I was still officially an university student and had free access to all the papers from most organizations like ACM or IEEE, caused that I started reading more and more papers on the topic, learning about the history, how the systems evolved, and so on.

Many ideas were based on a paper by Yan Ke, Derek Hoiem, and Rahul Sukthankar called "[_Computer Vision for Music Identification_](http://www.cs.cmu.edu/~yke/musicretrieval/)" (2005). In fact, even the [Last.fm fingerprinter](http://blog.last.fm/2010/07/09/fingerprint-api-and-app-updated) uses the code published by the authors of this paper. This is where I learned that audio identification is more about machine learning that it is about DSP. Many useful methods for extracting interesting features from audio streams are well-known and the problem is more about how to apply and index them the best way. The basic idea here is to treat audio as a spectral image and index the content of the image. I'll explain this in more detail and how Chromaprint uses this in a following post.

Another important paper for me was "[_Pairwise Boosted Audio Fingerprint_](http://dx.doi.org/10.1109/TIFS.2009.2034452)" (2009) by Dalwon Jang, Chang D. Yoo, Sunil Lee, Sungwoong Kim and Ton Kalker (Ton Kalker is a co-author of a historically important paper "[_Robust Audio Hashing for Content Identification_](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.16.2893)" (2001) published by Philips Research), which combined previous experiments of the authors with audio identification based on spectral centroid features and the indexing approach similar to the one suggested by Y. Ke, D. Hoiem and R. Sukthankar. For a long time this was the best solution I had and since it was actually not very hard to implement, the most time I spent on tweaking the configuration to get the best results.

The last major change came after I learned about "chroma" features by reading the "[_Efficient Index-Based Audio Matching_](http://dx.doi.org/10.1109/TASL.2007.911552)" (2008) by Frank Kurth and Meinard Müller. I've read more papers about chroma features later, but this was the first and also the most important one for me and some ideas about processing the feature vectors from it are implemented in Chromaprint. Chroma features are typically used for music identification, as opposed to audio file identification, but I tried to use them with the approach I already had implemented and it nicely improved the quality of the fingerprinting function and actually reduced complexity which allowed me to use much larger training data sets.

Anyway, this is more or less how I got to this point. As I mentioned, I'll try to describe in more detail how Chromaprint works and where are the exact ideas from in an another post later. The code is not finished yet, but the core ideas are already implemented and tested. The work that has to be done is mostly about cleaning the code, tweaking the configuration, running the learning algorithm on a better training data set (as I used only random selections of my music collection so far) and building some API that can be used by external applications.

The code is written in C++, but I plan the public API to be in plain C. Except for a [FFT](http://en.wikipedia.org/wiki/Fast_Fourier_transform) library (either [FFTW3](http://www.fftw.org/) or [FFmpeg](http://www.ffmpeg.org/)), it has no external dependencies. It's released under the LGPL 2.1 license, so there should be no problem integrating it into a commercial application, assuming FFmpeg is used for FFT calculations (using FFTW3 would require the binary to be GPL compatible). The project is hosted on [Launchpad](https://launchpad.net/chromaprint) using Bazaar for development. I'm sorry I didn't include the complete development history there, but it's just full of junk commits, so you will not miss much. :)

What I'd really like is to start actually working also on the fingerprint lookup service, for which I need as many fingerprints as possible. I have a proof of concept written in Java, using PostgreSQL and the [intarray](http://www.postgresql.org/docs/8.4/static/intarray.html) extension, which allows me to search the fingerprints using [GIN indexes](http://www.postgresql.org/docs/8.4/interactive/gin.html). This works fine on a database with tens of thousands of fingerprints, but I'm not sure if it will scale to much higher numbers. If you would like to help and you are running Debian/Ubuntu Linux, please run these commands and [email me](mailto:lalinsky@gmail.com) the compressed `fpcollect.log` file:


    
    
    sudo apt-get install bzr cmake libfftw3-dev libavcodec-dev libavformat-dev libtag1-dev libboost-dev
    bzr branch lp:chromaprint
    cd chromaprint
    cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_TOOLS=ON .
    make
    ./tools/fpcollect /path/to/your/music/library >fpcollect.log
    



I'm sure the recipe should be easy enough to modify for other Linux distributions. I'll try to build a Windows binary in the next days.

_**UPDATE:** I've compiled a [Windows version of fpcollect](http://dl.dropbox.com/u/5215054/fpcollect-r8.zip). If you would like to help, please download it, change the `fpcollect.bat` file to point to your collection and run it. It should should produce a file called `fpcollect.log` like the Linux example above._

So that's all for now. My plans for near future are to clean up the library and build some simple GUI application for collecting fingerprints. Once this is done, I can ask non-programmers to help me build a test database of fingerprints (including MusicBrainz IDs) and work primarily on the server component. There are some things that could be improved on the client library from functional point of view, but I think it's good enough for now, so the server part seems more important at the moment.

Btw, I always considered "Chromaprint" to be a temporary name for the client library, not meant to be its final name. If you can think of a better name, ideally something that could be used also for the service/server, please let me know!

