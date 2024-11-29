---
comments: true
date: 2011-03-12 12:04:48
layout: post
slug: acoustid-moved-one-more-time
title: Acoustid moved one more time
wordpress_id: 1276
categories:
- acoustid
tags:
- acoustid
---

Last week, with the help from [Zvooq](http://zvooq.ru/), the Acoustid service was moved [one more time](/2010/12/acoustid-moved-to-a-new-server/), this time to a much more powerful server. This move made it possible to import all submissions from the backlog, so the database now contains fingerprints for over 5M unique tracks. Unfortunately, only around 2.4M of them are matched to the MusicBrainz database, but I'm hoping that the ratio will improve soon.

There are a couple of things on my roadmap:

  1. Get rid of all Java server code and replace it with Python with C modules, more tightly integrated to Chromaprint. This should make it easier to start adding features to the webservice.
  2. Finally start returning MB metadata in the webservice. Currently, the web service only returns MBIDs, so potential applications have to use the MB webservice to get metadata. Acoustid should release at least some metadata, so that applications can filter the results without querying MB.
  3. Write a plugin for Picard that enables it to query Acoustid instead of MusicDNS. This will require a lot of work on Picard's internals, but it's probably the easiest way to realistically test Acoustid.

I could really use some help with this, so if you are interested, please let me know!
