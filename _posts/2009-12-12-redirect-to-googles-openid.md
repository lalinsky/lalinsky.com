---
comments: true
date: 2009-12-12 07:02:27
layout: post
slug: redirect-to-googles-openid
title: Redirect to Google's OpenID
wordpress_id: 241
categories:
- tools
tags:
- google
- openid
- xrds
---

I've started using OpenID some time ago and I really like it. I was surprised that large companied like [Google](http://code.google.com/intl/sk/apis/accounts/docs/OpenID.html) or [Yahoo!](http://openid.yahoo.com/) are OpenID providers and that made me to try using Google's OpenID. The first site I logged in to was [Stack Overflow](http://stackoverflow.com/), which has nice buttons for major providers, so that was easy. The problem was when I first needed to log in to a site without such buttons. After some searching I've found out that the Google OpenID end-point is [https://www.google.com/accounts/o8/id](https://www.google.com/accounts/o8/id), but I had to search for it every single time I needed it. Every time I find it I think it shouldn't not that hard to remember the URL, but the next time I need it I just can't remember it.

So I thought about switching to using my own URL as my OpenID, but I didn't want to run my own provider server. OpenID supports delegation, so normally it would be a matter of adding two lines of HTML code to the header of this blog and I could use Google's OpenID server with my own URL. The issue is with the way Google handles identities. The main URL [https://www.google.com/accounts/o8/id](https://www.google.com/accounts/o8/id) is the same for everyone and Google will generate an unique OpenID for every combination of user and OpenID consumer. This is nice from privacy point of view, but it makes it impossible to use OpenID delegation, because in the delegation code I have to specify my OpenID. I can't do that if my OpenID is different for every site I log in to.

I ended up with just adding this to the static HTML file I have on [http://oxygene.sk/](http://oxygene.sk/):

    <meta http-equiv="X-XRDS-Location" content="https://www.google.com/accounts/o8/id" />

This means that every time I want to log in to an OpenID-enabled site, I can type in [http://oxygene.sk/](http://oxygene.sk/) and it will use the same Google's OpenID as before. Not exactly what I wanted initially, but it's better than having to remember the long URL.
