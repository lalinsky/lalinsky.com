---
comments: true
date: 2009-10-29 13:53:58
layout: post
slug: working-with-branches-in-bazaar
title: Working with branches in Bazaar
wordpress_id: 82
categories:
- tools
tags:
- bazaar
- bzr
- vcs
---

Many people dislike the directory-per-branch concept that [Bazaar](http://bazaar-vcs.org/) uses. What they don't realize though, is that this doesn't mean you need to have a working tree for each branch. You can very easily simulate cheap Git-style branches, but with some added flexibility. Checkouts are a fairly well known feature of Bazaar, but people mostly associate it with the centralized workflow (i.e. checking out remote branches). This is not the only use case for them.

When I work on larger projects, where I need multiple branches, I usually have a directory structure like this:

  * "project"
    * "branches"
      * "branchA"
      * "branchB"
      * "trunk"
    * "work"

In this example, "project" is a [shared repository](http://doc.bazaar-vcs.org/bzr.2.0/en/user-reference/bzr_man.html#repositories). It contains revisions for project's branches on a single place. The repository is created with the `--no-trees` option, so that working trees are not automatically for new branches. All the branches I need to work with are located in "project/branches/XXX". Thanks to the DAG model, they represent nothing more than pointers to the "head" revision in the repository, so they are pretty cheap to create.

My development happens in "project/work", which is a [lightweight checkout](http://doc.bazaar-vcs.org/bzr.2.0/en/user-reference/bzr_man.html#checkouts) to one of the branches. This means that it doesn't contain anything else but information about the state of the working tree and a pointer to the branch. For any operation, Bazaar will use the branch it points to instead.

I'll use QBzr as an example how to set this up:

    % bzr init-repo --no-trees qbzr
    Shared repository (format: 2a)
    Location:
    shared repository: qbzr
    % cd qbzr
    % mkdir branches
    % bzr branch lp:qbzr branches/trunk
    Branched 1032 revision(s).
    % bzr branch lp:qbzr/0.14 branches/0.14
    Branches 969 revisions(s).
    % bzr co --lightweight branches/trunk work
    % cd work


After doing this, I can work commit/pull/push in the "work" directory as if I was in the "trunk" branch. Nothing exciting. Let's say I want to fix a bug in the "0.14" branch:

    % bzr switch ../branches/0.14

Now I can work as if I was in the "0.14" branch. So I do some changes, commit them, do some more changes and realize that these should actually go to a new feature branch. So I'll not commit them and create the new branch (I use this very often, so I have `branch --switch` aliased to `sbranch`):

    % bzr branch --switch ../branches/0.14 ../branches/new-feature

At this point the "work" directory points to the the "new-feature" branch and the uncommitted changes are still there. So I can commit them, do some more work, merge from other branches, etc. While working on something, I might want to run code from two branches at the same time for comparison. This is where Git doesn't help you, because you can have only _one_ working tree at a time (unless you make a new clone of the repository). But with this layout in Bazaar, nothing says I can only have one checkout in the repository. I can actually have a checkout of one of the branches anywhere on the disk. So I do this:

    % cd ..
    % bzr co --lightweight ../branches/trunk tmp

And now I can run both versions from "work" and "tmp" side-by-side. After I'm done, I simply delete the "tmp" directory.

I'm writing this mostly because I'm surprised how little people know about it and I personally find it a very nice way to work in Bazaar.
