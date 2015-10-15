---
layout: post
title: Simple MP3 file downloader
category: d
tags: [python, software, rss, sublime text, feedparser, requests, bs4]
---

### Prerequisites
 > Sumlime text, python, requests, rss parser

First off, I had to know how to build python modules in sublime text. The followings are the guides from the official and [unofficial web sites](http://www.sublimetext.com/docs/build). With comprehension of these guides, I can write build script that fit.
 * [Understanding build system](http://www.sublimetext.com/docs/build)
 * [How to build](http://sublime-text-unofficial-documentation.readthedocs.org/en/latest/reference/build_systems.html)

That being said, in practice I searched google for some examples and found the following codes.

```javascript
{
    "cmd": ["<<<your path to python file>>>\\python.exe", "-u", "$file",],
    "file_regex": "^[ ]*File \"(...*?)\", line ([0-9]*)",
    "selector": "source.python"
}
```

> NOTE: <strong>$file</strong> is a variable used in sublime text build framework. It refers to the file in an active view. Aside from that, I still don't understand why do I need <strong>-u</strong> opriton for running a python module.

While I carry out a dry run for <strong>requests</strong> library with the following code, I got a <strong>UnicodeEncodeError</strong>. Until that point, I've misunderstood the python's text model. I considered the problem to be related with decoding of the characters. However, the characters are already stored into a string object called "r.text", which means those are already a unicode string. As it turns out, when I print out the string, the codec for output, which is 'cp949', tries to encode this string to that of suitable charset for Windows. In that procedure the following encoding error occured. For more information about codec, visit [Python official site](https://docs.python.org/3/library/codecs.html)

```python
# -*- coding: utf-8 -*-
import requests
rss = 'http://feeds.bbci.co.uk/learningenglish/english/features/6-minute-english/rss'
r = requests.get(rss)
print(r.text)
```

> UnicodeEncodeError: 'cp949' codec can't encode character '\xe2' in position 2347: illegal multibyte sequences

##### NOTE: A short summaray about python3's text model
```
1. An object of 'str' class is unicode.
2. The object can be encoded into 'bytes' with whatsoever character encoding is.
3. When you recieve bytes from outer spaces like web server, the encoding of bytes has to be known to convert it to a 'str' object.
4. Unicode Sandwich!
```

Fortunately, we don't have to print out all the lines of the received rss file. In addition to it, if I have to print out them all, 'try ... except' can be adopted.

Before starting writing codes to get the mp3 files from BBC Learning English programs, I was unware of the existance of 'feedparser'. If you use feedparser, it gets quite simple. For more information, visit [python wiki page](https://wiki.python.org/moin/RssLibraries) and [pypi](https://pypi.python.org/pypi/feedparser).


I managed to wirte a code for retrieving mp3 files, but the performance is so slow that I can take a long rest with my colleague after run this code. To remedy this, I decided to use threads.

```python
import requests
from bs4 import BeautifulSoup
import gc

rss = feedparser.parse('http://feeds.bbci.co.uk/learningenglish/english/features/6-minute-english/rss')

for item in rss.entries:
    p = requests.get(item.link)
    soup = BeautifulSoup(p.text, 'html.parser')

    try:
        link = soup.find('a', class_='bbcle-download-extension-mp3')['href']
        name = link.split('/')[-1]

        mp3 = requests.get(link)

        print(len(mp3.content))

        #with open(name, 'wb') as f:
        #   f.write(mp3.content)

        gc.collect()

    except TypeError as e:
        print('the page does not include a mp3 file')

    del(p, soup)
```

