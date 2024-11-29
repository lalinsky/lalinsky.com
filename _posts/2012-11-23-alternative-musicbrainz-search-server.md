---
layout: post
title: "Alternative MusicBrainz search server"
categories:
- musicbrainz
tags:
- musicbrainz
- search
- solr
- lucene
- mbslave
---

If you are working with a local MusicBrainz database and want to search in it,
there aren't that many choices. You can use the internal PostgreSQL full-text
search or you can use the official MusicBrainz search server.

I've been playing with the idea of a customizable Solr-based solution for a
long time. Creating a simple Solr index with the MusicBrainz data is not
hard, but keeping it up to date is more complicated. 

The [mbslave] package now includes a few scripts for creating and
updating a Solr search index. See the `README` file for more information on
how to set it up.

There are a few differences compared to the official MusicBrainz search server:

 - Everything is in a single index, so there is no need to tell it which entity you are interested in.
   If you want to restrict the search to a particular entity, use filter query (e.g. `fq=kind:artist`).
 - It's easy to extend the index to include more fields. There is no configuration file for this yet, but you can edit the [field definition] directly in the source code.
 - It doesn't store as much data as the MusicBrainz search server. The intended workflow is to use the database for displaying results.

I wrote it primarily for my own needs, but if it ends up being useful to people, I'll probably make it more configurable and move to a separate package as it does not necessarily depend on mbslave.

[mbslave]: https://github.com/lalinsky/mbslave
[field definition]: https://github.com/lalinsky/mbslave/blob/master/mbslave/search.py#L61

