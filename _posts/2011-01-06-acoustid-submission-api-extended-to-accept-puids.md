---
comments: true
date: 2011-01-06 16:27:47
layout: post
slug: acoustid-submission-api-extended-to-accept-puids
title: Acoustid submission API extended to accept PUIDs
wordpress_id: 1005
categories:
- acoustid
tags:
- acoustid
- chromaprint
- musicdns
- puid
- python
---

As strange as it might sound, the [Acoustid submission API](http://wiki.acoustid.org/wiki/Web_Service#Submission) can now also accept [PUIDs](http://musicbrainz.org/doc/PUID) instead of [MBIDs](http://musicbrainz.org/doc/Track_ID). I had the idea of using PUIDs to help bootstrap the Acoustid database for a long time, but I avoided implementing it, because I was afraid of bringing all the PUID↔MBID matching errors to the database. The topic came up yesterday and I realized that with having the audio fingerprints, MBIDs and MusicBrainz metadata in the same database, I can pretty easily remove any suspicious matches. So I've made two changes to the submission API that should make importing of untagged audio files easier:





  * Submissions can contain multiple MBIDs. This is useful if there is an external system for resolving some foreign fingerprints to MBIDs.


  * Submissions can omit MBIDs and submit a PUID instead. A file without any metadata can be then passed to [GenPUID](http://musicbrainz.org/doc/GenPUID) and the resulting PUID along with the Chromaprint fingerprint submitted. The server will then resolve the PUID using a local replicated MB database. The great thing is that this can be done for any random audio file.



I'm hoping to implement the second option in the Acoustid Fingerprinter, but for now Chromaprint's `fpcollect` has been modified to fingerprint also files without MBIDs and there are Python scripts that can get PUIDs from GenPUID and then submit them:


    
    
    ./tools/fpcollect -nombid /path/to/music/directory >fplog.txt
    ./tools/fillpuid.py -a <a href="http://ftp.musicbrainz.org/pub/musicbrainz/genpuid/keys.txt">MUSICDNS_APIKEY</a> -g /path/to/genpuid fplog.txt >fplog-puid.txt
    ./tools/fpsubmit.py -a ACOUSTID_APIKEY fplog-puid.txt
    



