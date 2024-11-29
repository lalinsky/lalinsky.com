---
comments: true
date: 2010-09-26 11:21:13
layout: post
slug: custom-file-associations-on-gnome
title: Custom file associations on GNOME
wordpress_id: 668
categories:
- tools
tags:
- emusic
- gnome
- linux
---

It seems that there is no GUI method settings associations for custom files in GNOME. I've had this problem before with [dbmodel](/projects/dbmodel/) before. It writes save its data in XML files that end with the extension ".dmf". The problem is that GNOME's file associations work with MIME types and by default GNOME doesn't know anything about these silly .dmf files, it only sees XML files, so if I want to automatically open .dmf files with dbmodel, I can either set it for all XML files or for nothing. I eventually gave up on trying to do this, but since I started to use eMusic and switched to Chrome, it become much more annoying.

The problem is that eMusic download files are also XML files, so I can't set the association just for ".emx" files. Another problem is that to open the files from Chrome, I can only use the default GNOME association. I can't use any other application without going to Nautilus and looking up the executable. The correct way to fix this seems to be to [add a new MIME type](http://library.gnome.org/devel/integration-guide/stable/mime.html.en):
    
    mkdir -p ~/.local/share/mime/packages
    vim ~/.local/share/mime/packages/emusic.xml
    update-mime-database ~/.local/share/mime/

Where the `emusic.xml` file contains the following:

    <?xml version="1.0" encoding="UTF-8"?>
        <mime-info xmlns="http://www.freedesktop.org/standards/shared-mime-info">
            <mime-type type="application/x-emusic">
            <comment>eMusic Download File</comment>
            <glob pattern="*.emx"/>
        </mime-type>
    </mime-info>

After running the commands, Nautilus identifies the .emx files as eMusic Download Files, so I can set to open them in the eMusic Download Manager, without touching the configuration for generic XML files.
