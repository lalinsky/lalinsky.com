---
comments: true
date: 2011-08-14 15:06:56
layout: post
slug: musicbrainz-acoustid-integration
title: MusicBrainz/Acoustid integration
wordpress_id: 1604
categories:
- acoustid
- musicbrainz
tags:
- acoustid
- javascript
- musicbrainz
- userscript
---

_**Update: **As of [Jan 12, 2012](http://blog.musicbrainz.org/?p=1244), Acoustid links are now displayed on MusicBrainz by default._

While the Acoustid database is reimporting, I wanted to do something fun, so tried to integrate Acoustid links into the MusicBrainz website. I wrote a simple [user script](http://userscripts.org/scripts/show/110183) that calls the Acoustid API, loads all IDs linked to a recording and displays the data right next to the list of PUIDs. If you install the script, the "PUIDs" page will now look like this:

[![](/uploads/mbacoustid-mb-300x230.png)](/uploads/mbacoustid-mb.png)

It links back to the Acoustid track pages, which you can see for example [here](http://acoustid.org/track/c149da2b-91fb-4253-80fe-c9645fe8e92f). The database is still very small, so you will probably not see the links too often, but that should get better soon.

The next step, which is probably not going to be this easy, will be adding the "Remove" links to the pages also for Acoustid tracks and actually making them work.
