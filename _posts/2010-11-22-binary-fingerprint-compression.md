---
comments: true
date: 2010-11-22 00:15:11
layout: post
slug: binary-fingerprint-compression
title: Binary fingerprint compression
wordpress_id: 884
categories:
- acoustid
- programming
tags:
- acoustid
- chromaprint
- compression
- fingerpriting
---

While working on the [Acoustid web service](http://wiki.acoustid.org/wiki/Web_Service), I had a hard time deciding how to send fingerprints to the server. The fingerprints are vectors of fairly large 32-bit numbers. Sending the numbers in binary (which would be ideal) is not easy, because almost all web standards expect textual data, if not plain ASCII. The usual trick is to [base64](http://en.wikipedia.org/wiki/Base64)-encode binary data, but that increases the size by 33 %, which wasn't acceptable for me. So I came up with the idea to compress the data using a special-purpose algorithm and then base64-encode the compressed data, and ideally also compress the base64-encoded data using [GZip](http://en.wikipedia.org/wiki/Gzip#Other_uses). The double-compression might seem weird, but it allows me to use only standard web tools and the resulting size is still smaller than using binary encoding.

So the problem was to find an appropriate compression algorithm for the fingerprints. The usual integer compression algorithms (Elias codes, Golomb coding, Rice coding) work well for small numbers. The most common use case for these algorithms are posting lists in an inverted index, where the document IDs can be sorted and simple [delta encoding](http://en.wikipedia.org/wiki/Delta_encoding) then produces small numbers that can be nicely compressed. The problem is that I had very large numbers and I couldn't sort them anyway. Using subtraction to calculate deltas wouldn't help me much, because the resulting numbers would be still too high. After some failed experiments with the standard approaches, I had one of those "light bulb moments": it occurred to me that even though the fingerprint numbers are high, they change slowly in terms in bit changes. If I use XOR instead of subtraction for the delta encoding, I will still get high numbers, but they will usually not have many bits set. What if I just take the bits that are set and encode those as a sequence of numbers between 1 and 32. It turns out that this works pretty well. Let's say we have a tiny fingerprint with four 32-bit numbers:


    
    0x7E65BC44 | 0x7E25B444 | 0x7E259414 | 0x6625D424



After applying the XOR encoding, we will get the following results:


    
    0x7E65BC44 | 0x400800 | 0x2050 | 0x18004030



The next step is to extract individual bits from these numbers:


    
    3 7 11 12 13 14 16 17 19 22 23 26 27 28 29 30 31 | 12 23 | 5 7 14 | 5 6 15 28 29



This doesn't seem like we achieved much, but if you look closer, you will notice that the numbers both small and increasing. Having large and randomly ordered numbers was the main reason for not being able to use traditional integer compression techniques. So, let's delta-encode the numbers:


    
    3 4 4 1 1 1 2 1 2 3 1 3 1 1 1 1 1 | 12 11 | 5 2 7 | 5 1 9 13 1



The result is a lot of very small numbers. That's exactly the right situation variable length integer encoding. I decided to use the [PFOR](http://oai.cwi.nl/oai/asset/15564/15564B.pdf)-like algorithm for this I'd still like to re-evaluate other variable length encodings, but PFOR was very simple to implement and worked better than any other alternative I'd tried. The basic idea is to use constant _n_-bit for encoding all the numbers, with an "exception" block after the main block for numbers greater or equal to _2n-1_. My version is different in that instead of storing the offset to the exception in the main block, I decided to always put the maximum possible value there. The current implementation in Chromaprint uses 3 bits, so the main block would contain these numbers, each of them encoded in 3 bits:


    
    3 4 4 1 1 1 2 1 2 3 1 3 1 1 1 1 1 0 7 7 0 5 2 7 0 5 1 7 7 1 0



And the exception block would only have numbers that were greater or equal to 7, with the 7 subtracted from the to fit in 5 bits each:


    
    5 4 0 2 6



In order to store this we need 31 * 3 + 5 * 5 = 118 bits, which is already a little less than the original binary encoding with 4 * 32 = 128 bits and it only gets better for larger fingerprints. In many cases, increasing the size of elements in main block to 4-bits also helps, so I'm planning to dynamically determine the ideal size and store in the fingerprint.

Here is a few examples of compression ratios on 120 seconds long fingerprints:




  


    Binary
    PFOR 3-bits
    PFOR 4-bits
    Binary + zlib
    PFOR 4-bits + base64 + zlib
  
  

3792
2604
2634
3465
2503

  

3792
2193
2160
2809
2072

  

3792
2430
2407
3409
2287

  

3792
2374
2343
3133
2221





I have implemented this algorithm first in [C++](http://bazaar.launchpad.net/~luks/chromaprint/trunk/annotate/head:/src/fingerprint_compressor.cpp) and then also ported to [Java](http://bazaar.launchpad.net/~luks/acoustid-server/trunk/annotate/head:/tools/src/main/java/org/acoustid/util/FingerprintCompressor.java) to use it on the server side.

