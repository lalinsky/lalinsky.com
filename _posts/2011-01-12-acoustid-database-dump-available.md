---
comments: true
date: 2011-01-12 21:38:51
layout: post
slug: acoustid-database-dump-available
title: Acoustid database dump available
wordpress_id: 1039
categories:
- acoustid
tags:
- acoustid
- database
- postgresql
---

I've finally written a script to take a consistent dump of the Acoustid database in the PostgreSQL tab-separated format used by the [COPY](http://www.postgresql.org/docs/8.4/static/sql-copy.html) command. I do not have any tools for importing it into PostgreSQL, so it has to be done manually by running SQL commands, but if anybody is interested in playing with the database, you can download it [here](http://acoustid.org/data/) (2.7G after compression using bzip2, 6.4G uncompressed). The data is licensed under a [Creative Commons BY-SA 3.0 License](http://creativecommons.org/licenses/by-sa/3.0/). I'll add a cron job to export the database at the beginning of every month.
