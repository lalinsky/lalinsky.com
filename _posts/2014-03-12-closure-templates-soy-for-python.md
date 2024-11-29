---
layout: post
title: Closure Templates (Soy) for Python
categories:
- programming
tags:
- templates
- soy
- closure
- python
- java
- javascript
---

For the past two weeks I've been adding a Python backend to Google's Closure Templates.
The work is now almost finished, so I'm happy to release a preview version of the
Soy-to-Python compiler with a runtime Python library ([closure-templates-for-python][])
and a Flask extension ([Flask-Soy][]), to make it possible to work with Soy templates
from Python.

There is a [Hello World Using Python][] page, modeled after the official documentation
for Java and JavaScript if you want to use Soy templates directly, and there is also
a separate [Flask-Soy documentation][] if you want to use them from a Flask application.
The source code is available at [Bitbucket][].

All of this code is just recently finished without any real-world use, so
there will be problems. I'm releasing it because I consider it functionally complete
and I'm now actually planning to start using it in a Python project, which will hopefully
help finding those problems. If you find any of them, please do let me know.

I have learned about Soy templates some time ago when working on my simple add-on
for Atlassian Stash. I found it really nice that the whole AUI widget library can be
used exactly the same way from Java as it is from JavaScript. I'm starting a new
project and Python being my language of choice, I was looking for a similar
cross-language templating system, but it seems there are not many options.

Some of the advantages of Soy templates are:

 * The same code can be used from Java, JavaScript and now Python.
 * The syntax suggests small, modular and well-documented templates.
 * Smart contextual auto-escaping.

The contextual auto-escaping is something I realized only after I started working
on this, but it's actually really really nice. You need to apply different escaping
in regular HTML text than in HTML attribute and yet another if you are
constructing a part of an URL. Soy templates do all of that automatically, so you
won't have a XSS vulnerability just because you forgot to use the right escape
function. This is really refreshing after seeing that most of the examples and demos
of JavaScript templating libraries are XSS vulnerable by default.

One big disadvantage of Soy templates is that you need to run Java in order to
compile your templates, but on the plus side you can pre-compile them, so you
don't necessarily need Java on your production servers.

*Note: It seems that even though the Closure Templates project has some code publicly
available, it is mostly developed internally and there also seems to be nobody at
Google responsible for the open source project at the moment, so I have no hope for
this code being accepted upstream. For that reason I tried to minimize changes to
the code base, to be able to maintain it as a "fork", so the Python backend is not
integrated with the rest of the code base as nicely as I'd like to. Just a warning
if you want to have a look. :)*

[Hello World Using Python]: https://bitbucket.org/lalinsky/closure-templates/wiki/Hello%20World%20Using%20Python
[Flask-Soy]: https://pypi.python.org/pypi/Flask-Soy
[Flask-Soy documentation]: https://pythonhosted.org/Flask-Soy/
[closure-templates-for-python]: https://bitbucket.org/lalinsky/closure-templates/downloads/closure-templates-for-python-latest.zip
[Bitbucket]: https://bitbucket.org/lalinsky/closure-templates/overview
