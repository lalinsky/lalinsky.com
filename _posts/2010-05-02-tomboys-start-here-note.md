---
comments: true
date: 2010-05-02 15:13:27
layout: post
slug: tomboys-start-here-note
title: Tomboy's "Start Here" note
wordpress_id: 486
categories:
- tools
tags:
- dropbox
- gnome
- snippet
- tomboy
---

I use [Dropbox](http://www.dropbox.com/) to synchronize my [Tomboy](http://projects.gnome.org/tomboy/) notes. This works very well, but there is a problem when setting it up on a new computer. Tomboy has a special "Start Here" note, which is used mainly for organizing other notes. When I tell it to synchronize notes from the Dropbox directory and overwrite the existing default notes, it will do so, but it doesn't change it's "Start Here" note pointer. As as result, Tomboy is not aware that my new Start Here note is the one it should use. As far as I know, there is no way fix this using Tomboy itself. It can be done only using GConf. Here is an example how to do it from the command line:


    
    $ grep 'Start Here' -R ~/.local/share/tomboy/ -l
    /home/lukas/.local/share/tomboy/Backup/4a47410a-4976-4cb6-8ddc-fd744710dba7.note
    /home/lukas/.local/share/tomboy/<b>7d41fff6-6cae-44bc-87b9-6486c809e7ee</b>.note
    $ gconftool-2 --set /apps/tomboy/start_note --type string 'note://tomboy/7d41fff6-6cae-44bc-87b9-6486c809e7ee'
    




