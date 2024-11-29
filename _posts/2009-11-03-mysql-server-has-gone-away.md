---
comments: true
date: 2009-11-03 17:13:00
layout: post
slug: mysql-server-has-gone-away
title: '"MySQL server has gone away"'
wordpress_id: 134
categories:
- programming
tags:
- mysql
- mysqldb
- python
- twisted
---

I have written a few Twisted scripts at work that parse incoming data from a socket and save it in a MySQL database, using the [MySQLdb](http://mysql-python.sourceforge.net/) package. It's a well-known fact that the MySQL server will close connections that are inactive for some time and yet I forgot to handle it in last script I wrote. Previously I solved the problem by remembering the last time I used the connection and forcing a reconnect based on this value or the `recycle` option in SQLAlchemy's connection pool when I needed a connection pool (which does basically the same as the former). But when I found the problem in the latest script today, I thought I should finally solved it properly, so I started Googling...

I found out about the [`mysql_ping()`](http://dev.mysql.com/doc/refman/5.0/en/mysql-ping.html) function, which seemed perfect for this, especially in combination with the [`MYSQL_OPT_RECONNECT`](http://dev.mysql.com/doc/refman/5.1/en/mysql-options.html) option. The [MySQLdb User's Guide](http://mysql-python.sourceforge.net/MySQLdb.html) mentions a wrapper for `mysql_ping()`, but nothing about the `MYSQL_OPT_RECONNECT` option. It wouldn't be me if I didn't download the source code to check if there really isn't any way to set the option.

It turns out that the wrapper for `mysql_ping()` accepts a boolean argument, to set the option locally. It's even nicely documented in the [docstring for the method](http://mysql-python.sourceforge.net/MySQLdb-1.2.2/private/_mysql.connection-class.html#ping). Too bad I didn't look at the API documentation before reading the source code. :)

Anyway, I ended up with code like this and it seems to be working nicely:

    if self.db is None:
        self.db = MySQLdb.connect(...)
    else:
        self.db.ping(True)

