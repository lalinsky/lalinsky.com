---
comments: true
date: 2011-08-11 23:07:24
layout: post
slug: acoustid-database-rebuilding
title: Acoustid database rebuilding
wordpress_id: 1591
categories:
- acoustid
tags:
- acoustid
---

I've released a new version of the Acoustid server today. This release changes the way fingerprints are matched, so it required the database to be rebuilt, which means all previous "track IDs" will be lost. I'm expecting it will take at least a week before the database is back at the full size. This is probably the last rebuild and the IDs will stay stable from now on, but in case I discover some serious problems it might be necessary to do it one more time.

Here is a complete list of changes in this release:

  * Faster and more robust way of comparing fingerprints.
  * Mapping of fingerprints to PUIDs.
  * Initial support for storing custom foreign IDs.
  * Better tracking of the source for each fingerprint and fingerprint<->ID mapping.
  * Tracks now have UUIDs attached to them, so the default result ID format will change in a future version of the web service.

