---
layout: post
title: Twisted Trial and Jenkins
categories:
 - tools
tags:
 - python
 - twisted
 - jenkins
 - testing
 - ci
---

It's not completely obvious how to configure a Twisted-based job that uses [Trial] for running tests in Jenkins, so hopefully this post will save somebody a little time in the future.

Jenkins needs JUnit-style XML file to parse test results. You can get that output from Trial, if you pass the results thought [subunit] with [junitxml].

Debian-based distributions have those two modules packaged, so you can install them with `apt-get`.

    apt-get install python-subunit python-junitxml

Elsewhere you can use `pip` or `easy_install`.

    pip install python-subunit junitxml

Then in your Jenkins configuration, you can use the following command and let Jenkins know to collect the test results from `results.xml`.

    trial --reporter=subunit MYPACKAGE | subunit-1to2  | subunit2junitxml >results.xml

*UPDATE: compatibility with the latest subunit*

[subunit]: http://pypi.python.org/pypi/python-subunit
[junitxml]: http://pypi.python.org/pypi/junitxml
[Trial]: http://twistedmatrix.com/documents/current/core/howto/testing.html

