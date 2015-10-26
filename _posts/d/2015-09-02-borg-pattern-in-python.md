---
layout: post
title: Borg Pattern
category: d
tags: [python, software]
---

### Singleton pattern

[ActiveState's article about the singleton and the borg design pattern](http://code.activestate.com/recipes/66531-singleton-we-dont-need-no-stinkin-singleton-the-bo/)


Even though the singleton design pattern is more likely to draw attention due to its catchy name, is is not recommended to use this pattern, because it is focusing on identity rather than on state. Unlike this design pattern, the borg design pattern has all instances share state instead.

<div class="python_code">
<pre>
class Borg:
    __shared_state = {}
    def __init__(self):
        self.__dict__ = self.__shared_state
    # and whatever else you want in your class -- that's all!
</pre>
</div>

<div class="python_code">
<pre>
def borg(cls):
    cls._state = {}
    orig_init = cls.__init__

    def new_init(self, *args, **kwargs):
        self.__dict__ = cls._state
        orig_init(self, *args, **kwargs)
    cls.__init__ = new_init
    return cls


@borg
class Foo(object):

    def print_args(self):
        print('{}'.format(self.__dict__))
</pre>
</div>

You are also recommended to read further articles.
[stackoverflow](http://stackoverflow.com/questions/1318406/why-is-the-borg-pattern-better-than-the-singleton-pattern-in-python)
