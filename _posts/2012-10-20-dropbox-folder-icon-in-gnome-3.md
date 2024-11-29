---
layout: post
title: Dropbox folder icon in GNOME 3
categories:
 - tools
tags:
 - gnome
 - linux
 - dropbox
---

I upgraded to GNOME 3 just over a week ago and when installing the
[Feanza icon theme], I noticed that there is a nice Dropbox folder
icon included. Nautilus allows you to set icon for any folder,
but only lets you select a specific image file, which doesn't look
particularly good when scaled.

GNOME icon themes have icons in multiple sizes for each icon, so
rather then specific image files, it's better to use generic icon
names. I didn't find any GUI to assign an icon name to a folder,
but fortunately there is a way to do it using the
`gvfs-set-attribute` tool:

    gvfs-set-attribute ~/Dropbox metadata::custom-icon-name folder-dropbox

[Feanza icon theme]: http://tiheum.deviantart.com/art/Faenza-Icons-173323228

