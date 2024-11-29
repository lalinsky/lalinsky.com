---
comments: true
date: 2009-10-21 03:39:18
layout: post
slug: uuid-generator-in-plpgsql
title: UUID generator in PL/pgSQL
wordpress_id: 38
categories:
- programming
tags:
- database
- plpgsql
- postgresql
- musicbrainz
- sql
- uuid
---

While writing an [SQL script](http://bugs.musicbrainz.org/browser/mb_server/branches/RELEASE_20090524-BRANCH/admin/sql/updates/20090402-2.sql) to upgrade the MusicBrainz database for the last release, I needed a way to generate new [UUIDs](http://en.wikipedia.org/wiki/Universally_Unique_Identifier) from SQL. PostgreSQL has a native UUID data type and a contrib module for generating UUIDs since version 8.3, but this wouldn't help me, because I needed it to work with at least version 8.1. I had this idea to write PL/pgSQL functions to generate UUIDs, so I skimmer over the [RFC 4122](http://www.ietf.org/rfc/rfc4122.txt) that documents them and found out that it isn't actually that hard.

MusicBrainz uses random-based UUIDs (version 4) for all it's new IDs, so the first idea was to implement the same. I know I can't use this code in the end, because I need a good pseudo-random number generator, but I couldn't resist to write it anyway. Messing with bits in high-level languages is always fun :) Here is the result (_because of the use of the `random()` function, don't use the code for anything serious_):

    CREATE OR REPLACE FUNCTION generate_uuid_v4() RETURNS uuid
        AS $$
    DECLARE
        value VARCHAR(36);
    BEGIN
        value =          lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || '-';
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || '-';
        value = value || lpad((to_hex((ceil(random() * 255)::int & 15) | 64)), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || '-';
        value = value || lpad((to_hex((ceil(random() * 255)::int & 63) | 128)), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || '-';
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        value = value || lpad(to_hex(ceil(random() * 255)::int), 2, '0');
        RETURN value::uuid;
    END;
    $$ LANGUAGE 'plpgsql';

It turned out that we need deterministic IDs to be generated from the script, so V4 was out of question. That was good, because we would need a better PNRG for the final version.

The next idea was to create the URL on which the new rows will be server and generate name-based UUIDs using the URL namespace. The idea is to concatenate a namespace and a name, calculate a cryptographic hash of the result, and use it's bits to generate the UUID.  There are two options for hashing, either MD5 (version 3) or SHA-1 (version 5). SHA-1 is preferred by the RFC, but PostgreSQL only has a built-in function for MD5, so the decision for us was easy. The code doesn't depend any random numbers, so it's good enough to use in production.

    CREATE OR REPLACE FUNCTION from_hex(t text) RETURNS integer
        AS $$
    DECLARE
        r RECORD;
    BEGIN
        FOR r IN EXECUTE 'SELECT x'''||t||'''::integer AS hex' LOOP
            RETURN r.hex;
        END LOOP;
    END
    $$ LANGUAGE plpgsql IMMUTABLE STRICT;

    CREATE OR REPLACE FUNCTION generate_uuid_v3(namespace varchar, name varchar) RETURNS uuid
        AS $$
    DECLARE
        value varchar(36);
        bytes varchar;
    BEGIN
        bytes = md5(decode(namespace, 'hex') || decode(name, 'escape'));
        value = substr(bytes, 1+0, 8);
        value = value || '-';
        value = value || substr(bytes, 1+2*4, 4);
        value = value || '-';
        value = value || lpad(to_hex((from_hex(substr(bytes, 1+2*6, 2)) & 15) | 48), 2, '0');
        value = value || substr(bytes, 1+2*7, 2);
        value = value || '-';
        value = value || lpad(to_hex((from_hex(substr(bytes, 1+2*8, 2)) & 63) | 128), 2, '0');
        value = value || substr(bytes, 1+2*9, 2);
        value = value || '-';
        value = value || substr(bytes, 1+2*10, 12);
        return value::uuid;
    END;
    $$ LANGUAGE 'plpgsql' IMMUTABLE STRICT;

This code should be easy enough to modify to generate UUIDv5, if you have a way to calculate SHA-1 hashes. To use the function, you need to pass it a namespace and a name. The namespace itself is a UUID, it can be anything, but there are a few well-known options:

  * **URL**<br />`'6ba7b8119dad11d180b400c04fd430c8'`
  * **DNS** (fully-qualified domain name)<br />`'6ba7b8109dad11d180b400c04fd430c8'`
  * **ISO OID**<br />`'6ba7b8129dad11d180b400c04fd430c8'`
  * **X.500 DN** (in DER or a text output format)<br />`'6ba7b814-9dad-11d1-80b4-00c04fd430c8'`

The URL one is probably the most useful. So, to generate UUIDv3 for `http://www.example.com/foo/1`, you can use the following:

    SELECT generate_uuid_v3('6ba7b8119dad11d180b400c04fd430c8', 'http://www.example.com/foo/1');
