---
comments: true
date: 2011-03-29 08:03:16
layout: post
slug: easier-musicbrainz-ngs-database-setup
title: Easier MusicBrainz NGS database setup
wordpress_id: 1307
categories:
- musicbrainz
tags:
- musicbrainz
- ngs
- postgresql
- replication
---

[Some time ago](/2010/10/musicbrainz-database-replication/) I wrote a couple of tools that help me set up and update a mirror of the MusicBrainz database on the Acoustid server. It turned out to be work really well. Recently I've seen a few people struggling with setting up the NGS database using the original server codebase. The official route assumes you are going to run a MB server instance, which makes things a little bit more complicated than it has to be. You have to install a number Perl modules, you have to compile the MusicBrainz-specific PostgreSQL extensions, even though you most likely don't need them, you are forced to setup a `musicbrainz_db_raw` database that you are definitely not going to use, because there is no data in it, etc.

If you just want to have a local copy of the MB NGS database, don't want to mess with CPAN or compiling PostgreSQL extensions, you can try the [NGS version of mbslave](https://github.com/lalinsky/mbslave). You only need Python, psycopg2 and PostgreSQL. The number of steps you have to do manually is probably larger than with the official way, but on any Unix-based OS it should be just copy&paste; from the [README](https://github.com/lalinsky/mbslave/blob/master/README.txt) file and it should really just work, with the benefit of being able to customize everything.
