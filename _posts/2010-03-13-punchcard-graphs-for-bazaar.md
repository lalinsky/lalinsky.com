---
comments: true
date: 2010-03-13 10:23:26
layout: post
slug: punchcard-graphs-for-bazaar
title: '"Punchcard" graphs for Bazaar'
wordpress_id: 421
categories:
- tools
tags:
- bzr
- github
- musicbrainz
- picard
- qbzr
---

GitHub has a nice feature called "Punchcard". It's a graph that represents numbers of commits by day and hour ([example](http://github.com/lalinsky/musicbrainz-server/graphs/punch_card)). You can easily see wherether a project was hacked over weekends or nights, if it's done by full-time employees, etc. There are two problems though:

  1. It ignores timezones. All timestamps are treated as if they were somewhere in the US. For projects where committers live across the world (most open source projects) it's therefore almost useless.
  2. It only works for GitHub projects, obviously.

I've put together a little Bazaar plugin that can generate similar graphs for Bazaar branches. You can get the plugin [here](http://bzr.oxygene.sk/bzr-plugins/punchcard). It requires PyQt for image drawing, but many Bazaar users should already have PyQt installed. Once you install the plugin, you can generate a "Punchcard" by running a command like this in a branch directory:
    
    bzr punchcard project-punchcard.png
    
For example, for [QBzr](http://wiki.bazaar.canonical.com/QBzr) you will get this graph:
[![](/uploads/punchcard-qbzr-2010-03-13.png)](/uploads/punchcard-qbzr-2010-03-13.png)

You can see that the project is done by people who obviously have too much free time. There is activity pretty much any time. I guess only on weekend nights, people are tired and enjoy some sleep. :)

On the other hand, [Picard](http://musicbrainz.org/doc/PicardTagger) was hacked by people with good sleep patterns over evenings and weekends:
[![](/uploads/punchcard-picard-2010-03-13.png)](/uploads/punchcard-picard-2010-03-13.png)

[MusicBrainz Server](http://musicbrainz.org/) is in a similar situation, minus the good sleep patterns:

[![](/uploads/punchcard-mbserver-2010-03-13.png)](/uploads/punchcard-mbserver-2010-03-13.png)

The last example is [Bazaar](http://bazaar.canonical.com/en/). It's mostly written by full-time employees and it shows:
[![](/uploads/punchcard-bzr-2010-03-13.png)](/uploads/punchcard-bzr-2010-03-13.png)
