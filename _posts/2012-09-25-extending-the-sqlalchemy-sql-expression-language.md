---
layout: post
title: Extending the SQLAlchemy SQL expression language
categories:
 - programming
tags:
 - python
 - sqlalchemy
 - sql
 - database
 - oracle
---

The SQL expression language from SQLAlchemy is already very flexible and
allows you to build almost any standard SQL query, but sometimes you just
need to use a SQL extension that isn't supported by SQLAlchemy.

Suppose I have a very simple music datababse and I want a list of all artists
with their latest album name. Oracle has special aggregate functions
[`FIRST`/`LAST`][ora_first], which together with the `KEEP` clause can help with queries like
this, but unfortunately it isn't supported by the Oracle dialect in SQLAlchemy.
The SQL query would look like this:

```
SELECT
    artist,
    MIN(name) KEEP (DENSE_RANK LAST ORDER BY release_date)
FROM album
GROUP BY artist
```

Recently, I've learned that it's possible to extend the [SQL compiler][sqla_comp] and
add support for custom clauses that SQLAlchemy doesn't understand natively.
In my specific case of the `KEEP` clause, I need this code to make it work:

```
import itertools
from sqlalchemy.util import to_list
from sqlalchemy.sql.expression import ColumnElement, ClauseList
from sqlalchemy.ext.compiler import compiles

class Keep(ColumnElement):

    def __init__(self, func, order_by, first=True):
        super(Keep, self).__init__()
        self.func = func
        self.order_by = ClauseList(*to_list(order_by))
        self.first = first

    @property
    def type(self):
        return self.func.type

def keep_first(func, order_by):
    return Keep(func, order_by)

def keep_last(func, order_by):
    return Keep(func, order_by, first=False)

@compiles(Keep)
def compile_keep(keep, compiler, **kwargs):
    return "%s KEEP (DENSE_RANK %s ORDER BY %s)" % (
        compiler.process(keep.func),
        "FIRST" if keep.first else "LAST",
        compiler.process(keep.order_by)
    )
```

With this, I could simply use `keep_first()` and `keep_last()` as any
other SQL expression functions. For example, the above SQL query would
have been written like this:

```
session.query(Album.artist,
              keep_last(sql.func.min(Album.name), Album.release_date)).\
    group_by(Album.artist)
```

The main advantage of having it all written as a SQL expression is that
I don't need to know the actual column names, which get more and more
complicated once you have some joins and subqueries in the SQL expression.

[sqla_comp]: http://docs.sqlalchemy.org/en/rel_0_7/core/compiler.html
[ora_first]: http://docs.oracle.com/cd/B19306_01/server.102/b14200/functions056.htm

