---
comments: true
date: 2009-12-16 15:33:40
layout: post
slug: resizing-a-lvm-swap-volume
title: Resizing a LVM swap volume
wordpress_id: 238
categories:
- tools
tags:
- linux
- lvextend
- lvm
- snippet
- swap
---

Another post mainly for myself, just so I know where to find the information quickly the next time I need it. If you have swap on a LVM volume, these commands can be used to resize it (in this case, increase by 100MB):

    
    swapoff /dev/vg_foo/lv_swap
    lvextend -L+100M /dev/vg_foo/lv_swap
    mkswap /dev/vg_foo/lv_swap
    swapon /dev/vg_foo/lv_swap



That is: disable swapping on the volume, extend it, re-create the swap area and enable swapping again.
