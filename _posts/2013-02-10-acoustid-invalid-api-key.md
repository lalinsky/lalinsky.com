---
layout: post
title: "AcoustID: Invalid API key?"
categories:
- acoustid
tags:
- acoustid
---

Short version: I'm sorry, but today I had to block some applications from the AcoustID web service. If you were using AcoustID and now get an
"invalid API key" error, please [contact me](mailto:info@acoustid.org).

The long version is that when doing some maintenance today, I've found a few applications that are using the service against the usage guidelines.
I knew this would come and I would have to deal with it, I just didn't know when. When starting AcoustID, I have made it really easy to get new
API keys, in hope to avoid copying them
from other open source applications. I've also made it clear on the website, that the web service is intended for non-commercial use. If you
want to use it for commercial purposes, you can contact me and I'm sure we will find a way to make it work, but the web service is by default
intended for non-commercial users.

So I had to block a few applications and they should start receiving a HTTP 400 error, with the "invalid API key" error message. In order to
avoid this in the future, please register your application on the website and make sure it has a valid e-mail address entered. That way I can
contact you in case of a problem, rather than block the application.

