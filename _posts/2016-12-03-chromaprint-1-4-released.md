---
layout: post
title: Chromaprint 1.4 released
categories:
- acoustid
tags:
- acoustid
- chromaprint
- release
---

A new version of Chromaprint has been released. This is a fairly big release I originally intended to call 2.0,
but one key feature I was planning to include is not yet finished, so I decided to go with 1.4 instead.

So what's new?

The biggest feature is that all components of audio fingerprinting process now work in a streaming fashion and
can provide partial results at any time. That means that it's now possible to feed a continuous audio stream
to the process and get back partial fingerprints. This is useful if you want to fingerprint e.g. an internet radio stream
and do not want to explicitly split the stream into small chunks. There are also side effects of these changes and
the whole process is now faster and uses less memory.

This change is also reflected in the fpcalc utility, which can now work on streams.
In fact, the fpcalc utility has been completely rewritten. It now also supports JSON output for easier integration,
since you can find JSON parser in the standard library of almost every new programing language.

Here is an example using an online radio:

    $ fpcalc -ts -chunk 10 -overlap -json http://icecast2.play.cz/radio1.mp3
    {"timestamp": 1480789416.14, "duration": 12.60, "fingerprint": "AQAAUFSSTEnCRBKeZsR0..."}
    {"timestamp": 1480789423.68, "duration": 12.60, "fingerprint": "AQAAUZOSRIsUTkGz9SCZ..."}
    {"timestamp": 1480789433.68, "duration": 12.60, "fingerprint": "AQAAUYmWfJIoiNIRHzsl..."}

Or you can fingerprint raw pcm data from an external audio input:

    $ arecord --format=S16 --rate=44100 --channels=1 --file-type=raw \
        | fpcalc -format s16le -rate 44100 -channels 1 -ts -chunk 10 -overlap -json -
    {"timestamp": 1480789965.67, "duration": 12.60, "fingerprint": "AQAAUFqkMVmyJMHWBa97..."}
    {"timestamp": 1480789975.81, "duration": 12.60, "fingerprint": "AQAAUVGYSEkXJvit4ce1..."}
    {"timestamp": 1480789985.72, "duration": 12.60, "fingerprint": "AQAAUcoSpRunwN6Op_hy..."}

This opens up many options how Chromaprint can be used outside of AcoustID.

There has also been a big source code cleanup. A lot of old code has been removed. We started using C++11 features,
so it's no longer possible to compile Chromaprintwith old C++ compilers. Unit tests no longer depend on boost.
KissFFT is now bundled in the package and used as a fallback if no other FFT library is found. That means Chromaprint can be now
build without any external dependencies.

The public C API now uses standard fixed-size int types from stdint.h. This breaks API-level backwards compatibility,
you will need to modify your programs. The binary interface has not changed and programs compiled against the old version of
the library will continue working.

All source code from Chromaprint written by me has been relicensed from LGPL to MIT. Note that this does not mean much for the
library as a whole, since it still depends on LGPL code, but it's now easier to reuse parts of the code in other projects, if needed.

We also have fpcalc binaries for ARM processors now. They were built and tested on Raspberry Pi, but might work on other ARM devices.

Download:

* [Source code tarball](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-1.4.tar.gz) (597 KB)
* Static binaries for the `fpcalc` tool
  * [Windows, i686](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.4-windows-i686.zip) (1.4 MB)
  * [Windows, x86\_64](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.4-windows-x86_64.zip) (1.5 MB)
  * [macOS, i386](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.4-macos-i386.tar.gz) (1.1 MB)
  * [macOS, x86\_64](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.4-macos-x86_64.tar.gz) (1.2 MB)
  * [Linux, i686](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.4-linux-i686.tar.gz) (1.3 MB)
  * [Linux, x86\_64](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.4-linux-x86_64.tar.gz) (1.2 MB)
  * [Linux, armhf](https://bitbucket.org/acoustid/chromaprint/downloads/chromaprint-fpcalc-1.4-linux-armhf.tar.gz) (1.2 MB)
