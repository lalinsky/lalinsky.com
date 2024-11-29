---
comments: true
date: 2011-04-23 10:58:56
layout: post
slug: making-fail2ban-with-ipfw-firewall-on-freebsd-work
title: Making Fail2ban with IPFW firewall on FreeBSD work
wordpress_id: 1357
categories:
- tools
tags:
- fail2ban
- firewall
- freebsd
- ipfw
---

The internet is a nasty place, everybody is trying to hack into your servers if they are publicly accessible. Even though I always disable password authentication, so there is very little chance somebody could "guess" my private RSA key, I don't like `/var/log/auth.log` being spammed. [Fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page) is a nice solution to that I use on Linux with iptables, but it was not working for me on FreeBSD with IPFW.

My firewall is configured to drop everything by default, so it needs to have rules with connections that it should be allowed. However, the default IPFW ban action looks like this:


    
    
    actionban = ipfw add deny tcp from <ip> to <localhost> dst-port <port>
    



This inserts the rule at the end of the rule list, just before the default rule. This doesn't work for me, because it's after my own rule rule that allows traffic on that port from any address, so the newly added rule has no effect. I ended up modifying the IPFW action configuration file (`$PREFIX/etc/fail2ban/action.d/ipfw.conf`) to dynamically generate rule numbers that are before my own rules:


    
    
    actionban = USEDNUMS=`ipfw list | perl -pe 's/(\d{5}) .*\n/$1|/' | perl -pe 's/\|$//'`
                NUM=`jot -w '%%05d' - <minnum> <maxnum> | grep -vE "($USEDNUMS)" | head -n1`
                ipfw add $NUM deny tcp from <ip> to <localhost> dst-port <port>
    



This only uses rule numbers between `<minnum>` and `<maxnum>`, selecting the first one that is available. I can set the variables in my `jails.conf` configuration like this:


    
    
    [ssh-ipfw]
    
    enabled  = true
    filter   = sshd
    action   = ipfw[localhost=any,minnum=01110,maxnum=01199]
    logpath  = /var/log/auth.log
    ignoreip = 168.192.0.1
    



The default unban action is also problematic. I was testing the above from a server that has some traffic on another port explicitly allowed. However, when canceling the SSH ban, Fail2ban deleted also my own rule because it was using only the IP for firewall rule matching. Changing it like this works for me, but it's still not ideal:


    
    
    actionunban = ipfw delete `ipfw list | grep -i 'deny tcp from <ip> to <localhost>' | awk '{print $1;}'`
    



Ideally I would like to check also the port numbers, but I can't find an easy way to resolve the service name to a port number.

The final version of the IPFW Fail2ban action configuration file that I'm using is [here](http://dl.dropbox.com/u/5215054/ipfw.conf).
