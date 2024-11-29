---
layout: post
title: TagLib 1.8 released
categories:
- taglib
tags:
- release
- taglib
---

I'm sorry this took longer than expected, but I've just released the
final version of TagLib 1.8. There are a few changes compared to the
beta version, see the change log below.

Download:

  * [Source Code Tarball](https://github.com/downloads/taglib/taglib/taglib-1.8.tar.gz)

If you find any problems, please report them at the [GitHub bug tracker](https://github.com/taglib/taglib/issues).

Changes in 1.8 final:

- Added support for OWNE ID3 frames.
- Changed key validation in the new PropertyMap API.
- `ID3v1::Tag::setStringHandler` will no londer delete the previous handler, the caller is responsible for this.
- File objects will also no longer delete the passed IOStream objects. It should be done in the caller code after the File object is no longer used.
- Added `ID3v2::Tag::setLatin1StringHandler` for custom handling of latin1-encoded text in ID3v2 frames.
- Fixed validation of ID3v2 frame IDs (IDs with '0' were ignored).

Changes in 1.8 beta:

- New API for accessing tags by name.
- New abstract I/O stream layer to allow custom I/O handlers.
- Support for writing ID3v2.3 tags.
- Support for various module file formats (MOD, S3M, IT, XM).
- Support for MP4 and ASF is now enabled by default.
- Started using atomic int operations for reference counting.
- Added methods for checking if WMA and MP4 files are DRM-protected.
- Added `taglib\_free` to the C bindings.
- New method to allow removing pictures from FLAC files.
- Support for reading audio properties from ALAC and Musepack SV8 files.
- Added replay-gain information to Musepack audio properties.
- Support for APEv2 binary tags.
- Many AudioProperties subclasses now provide information about the total number of samples.
- Various small bug fixes.

