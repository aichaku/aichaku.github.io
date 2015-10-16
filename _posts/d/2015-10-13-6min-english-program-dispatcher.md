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

Before adopting threads to this application, profiling performance would be a good start point to figure out what is wrong behind the code. After running profiling, the following result is emitted.

 * [Python profilers](https://pymotw.com/2/profile/)

```
ncalls  tottime percall cumtime percall filename:lineno(function)
105179  490.659 0.005   490.659 0.005   {method 'recv_into' of  '_socket.socket'    objects}
114     36.274  0.318   36.275  0.318   {method 'connect'   of  '_socket.socket'    objects}
114     1.871   0.016   1.876   0.016   {built-in   method  getaddrinfo}
63978   0.463   0       2.225   0       parser.py:360(parse_starttag)
48440   0.43    0       348.58  0.007   {function   HTTPResponse.read   at  0x020BDC90}
57      0.394   0.007   3.962   0.07    parser.py:193(goahead)
48440   0.34    0       347.912 0.007   {method 'readinto'  of  '_io.BufferedReader'    objects}
451569  0.335   0       0.335   0       {method 'match' of  '_sre.SRE_Pattern'  objects}
913     0.319   0       350.123 0.383   {method 'join'  of  'bytes' objects}
48549   0.317   0       349.529 0.007   response.py:205(read)
52      0.274   0.005   0.274   0.005   {method 'write' of  '_io.BufferedWriter'    objects}
174807  0.248   0       0.252   0       element.py:189(setup)
105179  0.24    0       491.237 0.005   socket.py:357(readinto)
48440   0.227   0       348.151 0.007   client.py:520(readinto)
64035   0.204   0       0.598   0       element.py:779(__init__)
955326  0.203   0       0.429   0       {built-in   method  isinstance}
63978   0.191   0       1.138   0       __init__.py:386(handle_starttag)
56507   0.171   0       0.809   0       parser.py:463(parse_endtag)
49654   0.169   0       0.313   0       _collections_abc.py:422(get)
129331  0.164   0       0.528   0       __init__.py:287(endData)
39384   0.163   0       0.247   0       __init__.py:148(_replace_cdata_list_attribute_values)
105179  0.155   0       0.155   0       {method '_checkClosed'  of  '_io._IOBase'   objects}
181965  0.153   0       0.153   0       {method 'search'    of  '_sre.SRE_Pattern'  objects}
247523  0.149   0       0.206   0       _markupbase.py:48(updatepos)
135456  0.146   0       0.226   0       abc.py:178(__instancecheck__)
48550   0.141   0       348.724 0.007   client.py:489(read)
48662   0.127   0       0.127   0       response.py:1(is_fp_closed)
63246   0.126   0       0.196   0       __init__.py:363(_popToTag)
50079   0.125   0       0.143   0       _collections.py:154(__getitem__)
63978   0.116   0       1.254   0       _htmlparser.py:52(handle_starttag)
48549   0.115   0       0.43    0       response.py:176(_init_decoder)
64035   0.112   0       0.131   0       __init__.py:278(pushTag)
48549   0.112   0       349.768 0.007   response.py:286(stream)
103466  0.11    0       0.777   0       element.py:1627(search)
57038   0.109   0       0.606   0       element.py:1586(search_tag)
105293  0.098   0       0.098   0       socket.py:398(readable)
1       0.093   0.093   537.655 537.655 Deaton.py:25(get_mp3)
```
This result showed that receiving web contents took a large portion due to network latency. So I concluded that adopting threads to carry out receiving data from network is quite reasonable. Thus, I wrote codes like below.

```python
# -*- coding: utf-8 -*-

import feedparser
import requests
from bs4 import BeautifulSoup
import gc
import cProfile
from threading import Thread

def do_cprofile(func):
    def profiled_func(*args, **kwargs):
        profile = cProfile.Profile()
        try:
            profile.enable()
            result = func(*args, **kwargs)
            profile.disable()
            return result
        finally:
            profile.print_stats()
    return profiled_func

def is_valid_file(length):
    if length < 5000000:
        raise TypeError

@do_cprofile
def get_mp3_(item):
    p = requests.get(item.link)
    soup = BeautifulSoup(p.text, 'html.parser')

    try:
        link = soup.find('a', class_='bbcle-download-extension-mp3')['href']
        name = link.split('/')[-1]

        mp3 = requests.get(link)

        is_valid_file(len(mp3.content))

        with open(name, 'wb') as f:
            f.write(mp3.content)

        del(mp3)

    except TypeError as e:
        print('the page does not include a mp3 file')

    del(p, soup)

@do_cprofile
def get_mp3():
    rss = feedparser.parse('http://feeds.bbci.co.uk/learningenglish/english/features/6-minute-english/rss')

    for item in rss.entries:
        t = Thread(target=get_mp3_, args=(item,))
        t.start()

if __name__ == '__main__':
    get_mp3()
```
<script src="https://gist.github.com/aichaku/c256fb6a996a6e41c95f.js"></script>

It showed better performance and I was satisfied, but still there would be some modification in the design of whole program in terms of maintenance or gui.