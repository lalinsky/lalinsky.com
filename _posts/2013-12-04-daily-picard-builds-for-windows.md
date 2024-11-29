---
layout: post
title: Daily Picard builds for Windows
categories:
- musicbrainz
tags:
- musicbrainz
- picard
- jenkins
- building
---

Building the Windows installer of MusicBrainz Picard has always been problematic. I never had the process
automated, it always involved a lot of manual steps which I could never remember. And because Picard releases
do not happen very often, it was not very easy for people to test with the latest version on Windows (which usually included a number of fixes.

After setting up the Jenkins for AcoustID builds, I've decided to re-use it for Picard builds as well.
For now it's running only daily builds, but it will also help with releases later. At the moment I do not have
a 24/7 running Mac machine, but in the future I'd like to have OS X builds set up in a similar way.

Anyway, if you want to test the development version of Picard on Windows, [here](http://build.oxygene.sk/job/package-picard-win-daily/) you can always find the latest version.

