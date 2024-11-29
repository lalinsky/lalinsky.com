---
comments: true
date: 2009-11-10 09:18:14
layout: post
slug: where-is-my-one-gig-of-ram
title: Where is my one gig of RAM?
wordpress_id: 195
categories:
- tools
tags:
- linux
- memory
- pae
- ram
- ubuntu
---

I got a [new laptop](http://www.alza.sk/lenovo-thinkpad-sl300-2743-abg-d118467.htm) last week and it came with an extra RAM module. I thought it would be fun to have more RAM in laptop than I have in my desktop machine, so I put it in and to my surprise Ubuntu was reporting only 3GB of RAM, even though the machine had 2x2GB modules. I checked BIOS and it correctly said the machine has 4GB of RAM. It turns out that on a 32-bit machine you can address only 3GB using the standard addressing method.  There is an extension to work it around, called [PAE](http://en.wikipedia.org/wiki/Physical_Address_Extension), but the default Linux kernel in Ubuntu has it disabled. I was afraid I'd have to compile my own kernel, but fortunately there is a package with PAE enabled, so I only had to do:

    
    sudo apt-get install linux-generic-pae


Reboot and woohoo, `/proc/meminfo` now shows the full 4 gigabytes.
