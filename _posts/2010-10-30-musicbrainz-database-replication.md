---
comments: true
date: 2010-10-30 11:06:17
layout: post
slug: musicbrainz-database-replication
title: MusicBrainz database replication
wordpress_id: 833
categories:
- musicbrainz
- tools
tags:
- database
- musicbrainz
- postgresql
- python
- replication
---

I wanted to work on the Acoustid lookup web service and for this I needed to setup a MusicBrainz replicated database slave. Normally I'd use the Perl code from mb_server, but I didn't want to mess up the setup I have on the machine hosting [acoustid.org](http://acoustid.org/) with CPAN packages. I had a plan to write a non-Perl version of the replicated code earlier, but only yesterday I really needed it. It turned out to be easier than I expected, so I already have a working version that I use to update my local MB database. [Get it from GitHub](https://github.com/lalinsky/mbslave), if you would like to give it a try.

It has scripts for importing the database dumps as well as applying replication packets, so it can be used creating a MB database from scratch as well as updating an existing one. Instructions for setting up a new database can be found in the [README](https://github.com/lalinsky/mbslave/blob/master/README.txt) file.

There are a few differences between these scripts and [mb_server](http://bugs.musicbrainz.org/browser/mb_server/branches/RELEASE_20090524-BRANCH/admin):

  1. Depends only on Python and [psycopg2](http://initd.org/psycopg/). Should be easy to set it up on non-Linux platforms, but I've not tested that.
  2. Doesn't require additional space for extracting the database dump tarballs. It decompresses and imports the files on-the-fly.
  3. Can be configured to import the MB database into any schema, not just "public".
  4. Can be configured to not replicate specific tables (e.g. direct search indexes, PUIDs).
  5. Regrouped transactions. The MB replication packet consists of multiple transactions (each of which has multiple database operations). The original replication code in mb_server imports the transactions exactly as they were executed in the master database. My code applies the whole packet in a single transaction. This is a small disadvantage, but from an external point of view (external scripts, triggers), there is no practical difference.
  6. New code, possibly buggy.

The code currently assumes that you know how to setup the MusicBrainz database, so it doesn't do many checks. It it turns out it's useful also to somebody else, I can make it more robust.

