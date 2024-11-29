---
comments: true
date: 2010-10-24 00:10:45
layout: post
slug: acoustid-fingerprinter-beta
title: Acoustid Fingerprinter (BETA)
wordpress_id: 789
categories:
- acoustid
tags:
- acoustid
- audio
- fingerprinting
- qt
---

[![](/uploads/AcoustidFingerprinter1-264x300.png)](/uploads/AcoustidFingerprinter1.png)

Since my last Acoustid-related post, I was working on a GUI application for submitting fingerprints. It's far from finished, but I think it has reached the state where it can do something useful without breaking on trivial problems. Those who used the old [Last.fm fingerprinter](http://www.flickr.com/photos/ario/1277562689/) will know where I got the inspiration for the UI. :)

There isn't much functionality. You copy&paste; your [Acoustid API key](http://acoustid.org/api-key), select which directories to scan and let the application do its job. It will go through the directories, look for audio files that it hasn't seen before, read MBID tags, calculate fingerprints and submit them to Acoustid. It remembers which files to ignore on the next run by using a simple text file with the list of processed files.

This is not an official release, so I don't have any packages yet (you can download [the source code](https://code.launchpad.net/~luks/acoustid-fingerprinter/trunk) using bzr if you are interested), but I'd love if somebody could give the [Windows version](http://dl.dropbox.com/u/5215054/fingerprinter-2010-10-23-1.zip) a try. I'm sure there will be some bugs, but after fixing them and releasing the fingerprinter app, I can finally ask non-technical people for help with submitting data and focus on the server side again.


