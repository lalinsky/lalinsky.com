---
comments: true
date: 2010-10-17 17:12:46
layout: post
slug: playing-with-irccloud
title: Playing with IRCCloud...
wordpress_id: 773
tags:
- chrome
- irc
- irccloud
- javascript
---

Yesterday I got my invite to [IRCCloud](https://irccloud.com/), which is a web-based IRC client, but unlike other similar clients, the IRC connection is on the server, so I stay connected even when I close my browser. The first thing I had trouble with, after switching from a customized IRSSI, was that all nicknames had the same color. I never realized how much I rely on nickname colors, but it seems that I'm too lazy to read the nicknames. I wanted to try writing some user scripts for Chrome and this seemed like a nice opportunity. I stole the coloring algorithm from [MusicBrainz' chat logs](http://chatlogs.musicbrainz.org/) and wrote [this little script](http://userscripts.org/scripts/show/88258), which automatically sets a different color for nicknames in IRCCloud:

[![](/uploads/irccloud-300x220.png)](/uploads/irccloud.png)

With this little hack, I can totally see myself moving away from IRSSI. IRCCloud seems a lot nicer than having to SSH to a server, even with the very basic functionality it has right now.
