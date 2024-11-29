---
layout: post
title: "AcoustID: Faster fingerprint importing"
categories:
- acoustid
tags:
- acoustid
- release
---

Asynchronous importing of user submissions was always a big part of the AcoustID architecture.
It makes things much easier to handle on the server side, allows database maintenance without
turning the service to read-only mode (just delays the imports) and has many other benefits.

However, people often wanted to get back AcoustIDs for fingerprints they just submitted. It took
about a minute to import the fingerprints, so the only solution was to wait a few minutes
and then look up the submitted fingerprint to see if it's already imported.

This weekend I've done some changes in how the importing process works and one of
the results is that fingerprints are imported within seconds, not minutes, after they
are submitted. During normal operation, this means that submitted fingerprints will
get imported practically immediately.

This change made it practical for the submission API call to wait a few seconds and
collect the results of the import process. There is a new parameter `wait` which, which
allows the client to set a timeout on how long is it willing to wait for the newly
imported AcoustIDs. If the fingerprints are imported within the timeout, it will
return the AcoustIDs and set the submission status to "imported". Otherwise, it will
just return the submission ID and the status as "pending". These IDs can be used to
look up the status later.

See the documentation on the [`submit`][submit] and [`submission_status`][submission_status] API calls for details.

[submit]: http://acoustid.org/webservice#submit
[submission_status]: http://acoustid.org/webservice#submission_status

