---
comments: true
date: 2009-11-16 18:26:04
layout: post
slug: bazaar-commit-metadata
title: Bazaar commit metadata
wordpress_id: 164
categories:
- tools
tags:
- bazaar
- bzr
- qbzr
---

I maintain a few open source projects that use SVN, so notes like "Fixes bug #123, patch by J. Random Hacker" in commit messages are more than usual. When I started using Bazaar for Picard, I thought it would be nice to handle these natively. Bazaar could store bug metadata since [version 0.16](http://doc.bazaar-vcs.org/bzr.2.0/en/release-notes/bzr-0.16rc1.html), using the `bzr commit --fixes` option, so that was nice. It kind of inspired me to add the other part, the author name, which was a little more important for me than bug numbers. I wanted contributors who send plain patches to be equally credited for their work in the default branch viewing tools. I knew that Git had the concept of separated "committer" and "change author" and I really liked the idea, so I submitted a patch to Bazaar to add something similar (that was in [bzr 0.91](http://doc.bazaar-vcs.org/bzr.2.0/en/release-notes/bzr-0.91rc1.html)). The change allows you specify the author name on commit, that would be stored along in the revision along with the committer name. So you can run a command like this:

    bzr commit --author "J. Random Hacker <jr@example.com>" --fixes project:123 -m "Blah, blah, ..."

And then see the author name in other tools like `bzr log` or `bzr annotate`:

    ------------------------------------------------------------
    revno: 1
    author: J. Random Hacker <jr@example.com>
    committer: Lukáš Lalinský <lalinsky@gmail.com>
    [...]

Naturally, commit, log and annotate from QBzr also supported this since the day I wrote the patch. It's a shame that `bzr log` only displays author names, not the bug information, because that makes the useful feature quite hidden if you are not using any GUI plugin. I think QBzr users tend to use these features more often, because the commit dialog make it very visible that there is a possibility to do so, but also because `bzr qlog` will then nicely present the metadata (labels in the revision graph, clickable links, search for bug numbers, etc.). Here you can see an example with revision that fixes two bugs and the committer is different from the change author:

[![qlog-picard](/uploads/qlog-picard.png)](/uploads/qlog-picard.png)
