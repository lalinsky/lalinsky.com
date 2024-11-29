---
comments: true
date: 2009-12-06 05:56:49
layout: post
slug: file-names-and-unicode
title: File names and Unicode
wordpress_id: 226
categories:
- programming
tags:
- encoding
- filesystem
- qstring
- taglib
- unicode
- utf-8
---

Dealing with file names in a cross-platform application is not easy. A [question about using a file name in `QString` to create a new TagLib file](http://marc.info/?l=taglib-devel&m=126003964003689&w=2) came up on the TagLib development mailing list yesterday. The original problem was not related to Unicode, but after fixing one C++ issue, it ended up there. So, what was wrong?

`QString` represents an Unicode string. That is, an array of  [Unicode code-points](http://en.wikipedia.org/wiki/Code_point). The issue is that on most UN\*X platforms, filesystems are not aware of Unicode. File names are stored as an array of bytes. The filesystems don't care how are the bytes interpreted, but if applications want to display non-ASCII characters properly, they need to decode the bytes into Unicode. Since the filesystem itself doesn't know the encoding, it's necessary to look for the information somewhere else.

The user's locale is probably the first place to look. If the user uses some encoding for input/output, it's expectable that they use the same encoding for file names. This doesn't always have to be the case, so GNOME for example uses a special environment variable named [`G_FILENAME_ENCODING`](http://library.gnome.org/devel/glib/stable/glib-running.html#G_FILENAME_ENCODING). The problem is that all these solutions work globally for all filesystems. What if the main filesystem uses UTF-8 for everything, but the media player on which I sometimes upload files from Windows uses a different encoding? There is no way to tell applications that it should use CP-1250 for `/media/disk-1` and UTF-8 for everything else.

That's not everything though. Seeing broken characters is not nice, but not a blocking problem either. What if the application can't even read or write such files? That's a much larger issue. If the application is using Unicode to store file names, but it can't properly decode/decode the name, it won't be able to access the file. The obvious solution is to ignore Unicode and just use byte arrays. This would work fine on UN\*X, but new problems will show up if you are trying to write a cross-platform application. To be able to access all files on Windows, you have to do the exact opposite. You have to work with Unicode. On Mac you also have to work with Unicode, but it's even more interesting, because the filesystem will do Unicode normalization for you. There is no solution that works in all cases on all platforms.

To summarize the situation:

  * File names on UN\*X are byte arrays. You don't know their encoding, you can only guess. It's safest to not treat them as Unicode. If you want to treat them as Unicode, use functions like 	`[QFile::decodeName()](http://doc.trolltech.com/4.5/qfile.html#decodeName-2)` or `[g_filename_from_utf8()](http://library.gnome.org/devel/glib/unstable/glib-Character-Set-Conversion.html#g-filename-from-utf8)` to do the guessing for you.
  * File names on Windows are in Unicode. You can work with them using UTF-16.
  * File names on Mac are in normalized Unicode. You can work with them using UTF-8, but you can't just save any Unicode. The filesystem will normalize it to NFD for you.

It's sad to say, but I think this is one area where Windows is the nicest platform to deal with.
