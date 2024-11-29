---
comments: true
date: 2010-10-09 20:52:37
layout: post
slug: acoustid-updates
title: Acoustid updates
wordpress_id: 714
categories:
- acoustid
tags:
- acoustid
- chromaprint
- fingerprinting
---

Some news on the Acoustid project...



### New website



At first I wanted to do everything on the server side in Java, but as I was delaying it, I realized I better quickly just hack something up in PHP and then maybe write a better solution later. So I spent last weekend working on the website and came up with [this](http://acoustid.org/). Nothing fancy, but the I needed the website only for API key management.

It allows you to log in with either OpenID or your MusicBrainz username and password. Once you are logged in, it will show you your user API key that can be used for submitting data to the database. Additionally, users can register applications and get their application API keys. I'm thinking about adding some statistics once there is more data, but that's probably all the website has to do.



### Web service is now (partially) online



The Jave web service code has also been changed. I think I'm finally reaching a point where I'm happy with the design of both code and database structure. The web service is documented on [wiki](http://wiki.acoustid.org/wiki/Web_Service), but the XML for results with MusicBrainz metadata will probably change. Ideas on how to integrate the MusicBrainz XML schema are [welcome](http://wiki.acoustid.org/wiki/Mailing_List).

Anyway, since I had the website only and I was happy with the submission API, I've decided to put it online as well, so [`http://api.acoustid.org/submit`](http://api.acoustid.org/submit) is now accepting submissions as described on the wiki. It is hosted on a cheap VPS for now, but it should be enough for collecting the test data.

Some tweaking based on the collected data will have to be done before the lookup API can be enabled, but I hope it will follow shortly. Data dumps will be released as soon as I figure out a good way to do that (and there data to release of course!).



### Chromaprint API



A very important part of Chromaprint's algorithm is machine-learned configuration. When I asked people to [send me test fingerprints](/2010/07/introducing-chromaprint/) in July, it was using a configuration that was learned on a random selection from my music collection. That wasn't very good, so later I downloaded many 30 second audio snippets spanning many music genres from eMusic and other music websites and did some experiments with using this as the training data. It producer a little better results, but I still wanted something better. The problem was that I never had the time to do anything better. So I decided to take the best configuration I had and run another round of tests on real data.

Once I've done this decision, I needed to make a public API that can be used from external applications. The result is [here](http://bazaar.launchpad.net/~luks/chromaprint/trunk/annotate/head:/src/chromaprint.h). It's a plain C API that I hope should be possible to use from any programming language that can load a shared library. There is still no official Chromaprint release, but there will be one as soon as I successfully use the API to build the graphical fingerprint submission tool that I was planning to. I don't expected the API will change much though.



### Help needed!



Now I need to build a large database of fingerprint/MBID data to see how this all works in action. The last round helped me a lot, but I've made a big mistake of not collecting MusicBrainz identifiers along with the fingerprints. This has been fixed and the `fpcollect` tool from Chromaprint now extracts MB track IDs from files and writes it to the log file. If you are on Linux (or another platform that makes compiling from source code easy), please download and compile Chromaprint and run the fingerprinter on your music collection. On Debian/Ubuntu, something like this should do the trick:


    
    
    sudo apt-get install bzr cmake libfftw3-dev libavcodec-dev libavformat-dev libtag1-dev libboost-dev
    bzr branch lp:chromaprint
    cd chromaprint
    cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_TOOLS=ON .
    make
    ./tools/fpcollect /path/to/your/music/library >fpcollect.log
    



When you have the log file, you can run a simple Python application that is also available in Chromaprint to submit the data via the Acoustid web service. For this, you will have to first log in to the Acoustid website and [get an API key](http://acoustid.org/api-key). Once you have the key, you can run a command like this to submit the data (replace _XXX_ with your API key):


    
    
    ./tools/fpsubmit.py -a XXX fpcollect.log
    



I know the process isn't very easy, but I'm hoping to have a better and properly packaged submission tool soon.
