---
comments: true
date: 2010-01-23 04:00:38
layout: post
slug: mercurial-part-1
title: Mercurial, part 1
wordpress_id: 283
categories:
- tools
tags:
- fail
- hg
- mercurial
---

I've been using Mercurial at work for two months now and the expectations I had about it didn't change to anything better. I guess it looks cool for people who are used to SVN, or even CVS, and are not familiar with other DVCS. I must say that as a Bazaar user, I miss a lot of things. There are some that can be worked around, for something you just have to be extra careful and for something you are out of luck. I really don't get how people use Mercurial for managing large projects.

### Cheap Branches


One of the main reason people use DVCS is cheap branching. The most surprising thing for me was that Mercurial doesn't offer many choices. It doesn't have the concept of branches, like Bazaar or Git. All you have is an immutable repository that can contain multiple head revisions. By default, you are expected to clone the whole repository. As [I mentioned earlier](/2009/10/working-with-branches-in-bazaar/), I tend to work with many feature branches, so I prefer to have just one working tree.

One way to partially solve this problem is the [bookmarks extension](http://mercurial.selenic.com/wiki/BookmarksExtension). It basically allows you to have dynamic tags that move to new revisions as you commit. The initial configuration looks like this:

    
    [extensions]
    bookmarks =
    [bookmarks]
    track.current = True


The next step is to go to your clone and set the main/trunk/master/etc. bookmark:

    
    hg bookmark main


This is necessary, so that you easily know which revisions represents the project's "mainline". With the configuration I mentioned, the extension will track your current bookmark and update only that one. To set the current bookmark, you can use update:

    
    hg up -r main


It's important to always have the current bookmark set to _main_ whenever you are fetching changes from the mainline. Otherwise you will have to manually fix the bookmark's revision.

Creating new "feature branches" is easy then:

    
    hg bookmark -r main my-feature
    hg up -r my-feature


One important thing to remember is that after you use this, the repository will contain multiple heads. This means you can't use plain `hg push` to push changes to remote repositories. You always have to specify the revision, for example:

    
    hg push -r main https://hg.example.com/projects/trunk




### Merging


When it comes to merging, Mercurial doesn't make your life easier at all. The default assumption is that visual merging is the preferred way to go. Maybe I'm just too stupid, but I don't get visual merge tools. I just don't know how to use them. I prefer merging changes manually in my text editor, after I see the changes for the whole project. Mercurial would start up the merge tool for each file linearly, which means you don't have a global picture of impact on the project, when you are supposed to resolve a merge. I just can't work that way.

Fortunately, Mercurial does have an internal three-way merge algorithm, that can leave conflict markers in the merged files and let me do my job. You can configure it this way:

    
    [ui]
    merge = internal:merge


This actually uses the merge code from Bazaar, so I expected I'll be done quickly. There is one problem though. Instead of using patience diff, like Bazaar, it uses its own diff algorithm. The result is that in Bazaar the algorithm works just fine, in Mercurial it produces horrible results. That's actually pretty funny, considering the fact that I used ideas from Mercurial's diff code to implement the C version of patience diff in Bazaar.

Next step, the [Mercurial wiki](http://mercurial.selenic.com/wiki/MergeProgram) suggests `diff3` as a possible non-interactive merge tool. There are no examples how to configure it, but something like this technically works:

    
    [ui]
    merge = diff3
    [merge-tools]
    diff3.args = -m $local $base $other > $output


In my experience, this produces even worse results than `internal:merge`, so there is no reason to use it.

The last idea was to use the `merge` program from [RCS](http://www.gnu.org/software/rcs/) (yes, that's right, using an ancient VCS to fix issues in a "modern" VCS). I must say that installing RCS in 2010 feels weird, but as long as I can get my job done... Once it was installed, I used this configuration:

    
    [ui]
    merge = merge


I was surprised that the results were acceptably good. Not perfect, but I knew I can't expect more from a three-way merge. Ideally I'd like Bazaar's merge algorithm with patience diff, but I probably won't find time to port it, so `merge` will stay as my default merge tool for some time.

That's only the first part of the problem though. Now you have files with conflict markers and you need to resolve them. Based on my experience with other version control systems, I assumed conflicts would be listed in `hg status`, but I was wrong. They are nicely hidden here:

    
    hg resolve -l | grep '^U'


After manually resolving the conflicts, you can use the following command to mark the files as resolved:

    
    hg resolve -m path/to/file


_To be continued..._
