---
comments: true
date: 2009-12-26 12:09:29
layout: post
slug: using-utf-8-with-fithesis
title: Using UTF-8 with fithesis
wordpress_id: 252
categories:
- tools
tags:
- fi mu
- fithesis
- latex
- tex
---

My faculty has it's own [thesis LaTex style](http://www.fi.muni.cz/~xpavlov/fithesis/), which makes it very easy to get a decent looking thesis out of LaTeX without too much effort. The problem is that the style requires you to use ISO-8859-2 in your document, which is something I can't really live with. :) Here are instructions how to convert the style to UTF-8. I'm posting them here in hope that it will help some other student of [Faculty of Informatics, Masaryk University](http://www.fi.muni.cz/index.xhtml.en) in the future.

I'm doing this on an Ubuntu laptop, so first I'll need LaTeX:

    sudo apt-get install texlive

Then download fithesis:

    wget http://www.fi.muni.cz/~xpavlov/fithesis/install.sh
    wget http://www.fi.muni.cz/~xpavlov/fithesis/fithesis-0.2.12.tar.gz
    tar -zxvf fithesis-0.2.12.tar.gz
    cd fithesis-0.2.12

Convert the sources to UTF-8 and change the package options in the file:


    recode latin2..utf8 fithesis.dtx
    sed -i 's/latin2/utf8/' fithesis.dtx

And now we can install it:

    cd ..
    chmod a+x install.sh
    ./install.sh 0.2.12 /usr/share

At this point you can use the package in your UTF-8 encoded LaTeX document.
