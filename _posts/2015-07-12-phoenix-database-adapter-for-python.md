---
layout: post
title: Phoenix database adapter for Python
categories:
- programming
tags:
- python
- phoenix
- hbase
---

This is a small project I have been working on for a few weeks now. Mainly to get
familiar with the [Phoenix](http://phoenix.apache.org/) database, but also to just try
something different from what I do at work or my existing open source projects.

Phoenix is an SQL engine build on top of HBase and as it is typical in the Apache ecosystem,
all the existing tools expect you to use Java. Using a non-JVM language pretty much means you
are a second-class citizen and Phoenix is no an exception.

Fortunately, Phoenix does have a HTTP-based query server since version 4.4 and this server
could be used to access the database from another language. At the time I was looking at it,
there were no non-Java client libraries, so I wanted to see how hard it would be to write
one in Python.

So, after some digging through the source code and experimenting I was able to talk to the
server, and after more digging through the source code, testing, reporting and fixing bugs,
I can now release the first version of the Python package which makes it possible to do this:

{% highlight python %}
import phoenixdb

database_url = 'http://localhost:8765/'
conn = phoenixdb.connect(database_url, autocommit=True)

cursor = conn.cursor()
cursor.execute("CREATE TABLE users (id INTEGER PRIMARY KEY, username VARCHAR)")
cursor.execute("UPSERT INTO users VALUES (?, ?)", (1, 'admin'))
cursor.execute("SELECT * FROM users")
print cursor.fetchall()
{% endhighlight %}

You can install it from PyPI using `pip` or `easy_install` (preferably into a `virtualenv`):

    pip install phoenixdb

Please see the [documentation](https://pythonhosted.org/phoenixdb/) or check the [source code](https://bitbucket.org/lalinsky/python-phoenixdb) for more details on how to use it.

To experiment with the code, you will also need HBase with the Phoenix query server
running somewhere. This is not completely trivial to setup, but I have a Vagrant-based
environment for testing this and it's mentioned in the documentation.

Of course, nothing is perfect, and the query server is a pretty recent addition to Phoenix so there are problems
and with latest released version, you are pretty much restricted to the most basic data types for
numbers and text. Additionally, the remote protocol is still in development, so the library will need to keep
its releases synchronized with Phoenix releases. But it was a nice experiment and I'm quite happy with how far
did I managed to get.

One of my motivations for this was to try if Python could be realistically used to work with a large Phoenix database.
Maybe not as the primary way to talk to the database, because the server will probably always be just a second-class citizen,
but it could be useful for quick scripts where reliabiliy or performance are not that important.
