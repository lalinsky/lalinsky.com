---
comments: true
date: 2010-03-02 15:32:56
layout: post
slug: fun-with-timestamps
title: Fun with timestamps
wordpress_id: 342
categories:
- programming
tags:
- fail
- php
- stackoverflow
- timezone
---

Some programming languages really encourage using [UNIX timestamps](http://en.wikipedia.org/wiki/Unix_time) for working with dates. PHP is a good example of such a language. Functions like [`date`](http://php.net/manual/en/function.date.php), [`strtotime`](http://php.net/manual/en/function.strtotime.php), [`strftime`](http://php.net/manual/en/function.strftime.php) are used all the time. Most people don't realize that timestamps in general can't really be used for calculations though. The problem is that most countries use [daylight saving time](http://en.wikipedia.org/wiki/Daylight_saving_time), which means that two times a year the local timezone changes. This nicely breaks the assumption that every day has 24 hours. It doesn't. Sometimes it has 23 or 25 hours.

This [StackOverflow answer](http://stackoverflow.com/questions/2361222/php-date-function-finding-the-previous-week/2361335#2361335) is a nice example of the problem:


    
    
    $mondayStr = "last monday";
    if (date('N') !== '1') {  // it's not Monday today
        $mondayStr .= " last week";
    }
    
    $monday = strtotime($mondayStr);
    echo date('r', $monday);    // Mon, 22 Feb 2010 00:00:00 +1000
    
    $sunday = $monday + 86400 * 7 - 1;
    echo date('r', $sunday);    // Sun, 28 Feb 2010 23:59:59 +1000
    



The code seems logical. Get the timestamp of the last Monday, add 60 * 60 * 24 * 7 - 1 seconds and you have the end of Sunday. Works fine most of the time. Although, if the `$monday` happens to be _Mon, 22 Mar 2010 00:00:00_, the date that was supposed to be _Sun, 28 Mar 2010  23:59:59_ will actually be _Mon, 29 Mar 2010 00:59:59_. Why? Because 28 March 2010 has only 23 hours.

Never use timestamps to do calendar calculations. It's hard to get it right. If you really have to, at least use GMT timestamps.
