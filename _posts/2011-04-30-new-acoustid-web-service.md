---
comments: true
date: 2011-04-30 15:05:07
layout: post
slug: new-acoustid-web-service
title: New Acoustid web service
wordpress_id: 1420
categories:
- acoustid
tags:
- acoustid
- python
---

Some time ago I realized that having the server code in Java keeps me away from working more on it. The original reason for writing it in Java was that I expected it would do harder work, but I really don't need a "fast" language for simply running SQL queries. Additionally, since developing simple websites in Java turned out to be a pain, I used PHP to do the Acoustid website. I have no problem with PHP, but later I wanted to start integrating the website with the rest of the fingerprint database and it felt like a waste of my time to not share code, which is not easy if some parts are in Java, some in PHP. So, today I've released a new version of the server which is written completely in Python.

Everything should work as before, with one additional feature -- [new web service](http://acoustid.org/webservice). The v2 web service is very similar to the old one (which is also still fully functional), but can return response in the JSON format and finally can include MusicBrainz metadata. The data is represented in a NGS-like structure, to avoid any changes when MusicBrainz switches to NGS.

If you are familiar with MusicDNS's PUIDs, you know that lookups normally require two HTTP requests. One to get the PUID based on the fingerprint, another one to get the metadata based on the PUID. The nice thing about having both the fingerprints and the metadata in the same database is that we can avoid one of these requests, hopefully making the audio file identification experience faster. Using JSON instead of XML should also help, because it has to transfer significantly less data.

I think that this is a big milestone, because it makes the external interface almost complete. For occasional requests it should be also fast enough, but unfortunately it will handle higher traffic well. The top item on my roadmap right know is [making it much faster](https://github.com/lalinsky/acoustid-index), to enable easier scaling of the service by throwing more hardware at it.
