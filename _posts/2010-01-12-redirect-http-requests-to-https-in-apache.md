---
comments: true
date: 2010-01-12 14:20:26
layout: post
slug: redirect-http-requests-to-https-in-apache
title: Redirect HTTP requests to HTTPS in Apache
wordpress_id: 271
categories:
- tools
tags:
- apache
- mod_rewrite
- snippet
- web
---

One more short snippet, that might be useful to me in the future. If you have a website served by Apache and want your clients to only use HTTPS, you can use this [mod_rewrite](http://httpd.apache.org/docs/2.0/mod/mod_rewrite.html) configuration:


    
    RewriteEngine On
    RewriteCond %{HTTPS} off
    RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI} [L]



If the server then gets any HTTP request, it will redirect the client to the same URL, but with the `https` scheme. This means that actual requests will be only ever served via HTTPS.
