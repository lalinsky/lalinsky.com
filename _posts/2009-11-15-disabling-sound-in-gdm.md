---
comments: true
date: 2009-11-15 15:31:26
layout: post
slug: disabling-sound-in-gdm
title: Disabling sound in GDM
wordpress_id: 199
categories:
- tools
tags:
- fail
- gconf
- gdm
- gnome
- ubuntu
---

I often have to turn on my laptop in a situation when I need it to be quiet. It's easy to disable the login sound in GNOME, but in the new Ubuntu release it became quite hard to disable the GDM startup sound. Previously it was possible to simply use `gdmsetup` to change the sound, themes, etc. However, in recent versions of GDM (like the one included in Ubuntu 9.10), the window was reduced to a question whether it should log me in automatically or ask for the password. The old configuration file `gdm.conf` is also gone, replaced with GConf-based configuration. The [GDM documentation](http://library.gnome.org/admin/gdm/stable/configuration.html.en) says, as an example, that sound can be disabled by changing the `/apps/gdm/simple-greeter/settings-manager-plugins/sound/active` GConf key, so I tried to set it to `false`, but that didn't help. I've managed to fix it eventually, thanks to a [Ubuntu bug](https://bugs.launchpad.net/ubuntu/+source/gdm/+bug/437429), where somebody mentions the `/desktop/gnome/sound/event_sounds` key.

    
    sudo -u gdm gconftool-2 --set /desktop/gnome/sound/event_sounds --type bool false


I'm still not sure _why_ does it work and why does this key affect GDM, but as long as it's quiet...
