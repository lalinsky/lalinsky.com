---
comments: true
date: 2012-02-02 21:17:03
layout: post
slug: new-acoustid-server-release
title: New AcoustID server release
wordpress_id: 1744
categories:
- acoustid
tags:
- acoustid
- release
- server
---

I've just updated the AcoustID servers with the latest version of the code. There is a couple of changes on both the website and the API.

One important change on the website is addition of email and website fields to applications. If you are an author of an open source application that uses the AcoustID API, please fill them in so that I can contact you in case there is a problem with your API key. These fields are not displayed anywhere publicly.

Contributor statistics were also removed from the website. They were interesting when the project was small and had a couple of contributors, but recently I've had a increasing number of people asking to had their account removed from the statistics, so I've decided to remove the statistics completely.

The `/v2/lookup` method supports a new option `sources` in the `meta` parameter. It's only applicable when `meta` includes also `recordings` and it tells the server to return also the number of sources for each returned recording (corresponding to the Sources field on the website).

There are also two new methods `/v2/user/lookup?user=X` and `/v2/user/create_anonymous?client=X&clientversion;=X`, to support anonymous user accounts. They can be used from applications that can't ask the users to log in on the Acoustid website, but there are a number of rules that such applications should follow:

* The returned API keys must be stored in the user's settings and re-used. Do NOT create a new API key every time the application is started.
* You can't assume the saved API key still exists. Unused anonymous accounts will get deleted after some time. Use the user lookup call to verify the API key still exists.


