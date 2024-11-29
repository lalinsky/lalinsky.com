---
comments: true
date: 2011-11-10 13:21:58
layout: post
slug: acoustid-server-release
title: AcoustID server release
wordpress_id: 1639
categories:
- acoustid
tags:
- acoustid
- release
---

I guess it's time for AcoustID to have release notes when something gets released, so here is my first AcoustID server release blog post.

Some time ago I added overall search statistics. This release adds similar statistics for individual applications. For example:

[![](/uploads/acoustid_application_statistics-300x267.png)](/uploads/acoustid_application_statistics.png)

Of course, these statistics are only available to the owners of the applications.

Another change related to external application support is that the submission API call now accepts a new parameter `clientversion`. This is the first step to allowing a single application API key to be used by multiple versions of the application. In following releases I'll be changing the API key structure to allow applications to have a number of API keys that can be disabled/enabled any time.

