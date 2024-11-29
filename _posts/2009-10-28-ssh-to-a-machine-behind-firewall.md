---
comments: true
date: 2009-10-28 11:48:39
layout: post
slug: ssh-to-a-machine-behind-firewall
title: SSH to a machine behind firewall
wordpress_id: 73
categories:
- tools
tags:
- linux
- snippet
- ssh
- unix
---

_(This post is mostly for myself, because I know I'll forget the exact syntax next I need it)_

I've recently discovered that instead of ssh to one machine and then ssh to another machine in the local network, I can configure ssh to start the proxy connection automatically.  For example:

    Host example-local-10
        HostName 192.168.0.10
        ProxyCommand ssh example.com nc %h %p 2> /dev/null

Especially useful when working with various tools that just use the ssh transport protocol, like scp or rsync.
