---
layout: post
title: Daily C#
category: d
tags: [loop, repetition, array, Anonymous Object]
---

### Looping

C# provides same **for**, **do... while()**, **while** statements as those of C++. Adding to that it supports **foreach** looping.

```c#
int[] a = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 };

foreach (int i in a)
{
    Console.WriteLine("{0}", i.ToString());
}
```

 * TODO: Try to write examples in various cases and find out the underlying behavior.

### Array
Think of an array as a sequence of elements, all of which are the same type.

 * Sample code
```c#
int[] a = { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15 };

foreach (int i in a)
{
    Console.WriteLine("{0}", i.ToString());
}

for (int i = 0; i < a.Length; i++ )
{
    Console.WriteLine("{0}", a[i].ToString());
}

```

### Anonymous Object

Interestingly, the following expression is possible in C# like the similar expression is so in Javascript. I am geussing that the type of the object is randomly named by compiler.

```c#
var car = new { CodeName = "gorgeous lynda", Location = 4 };
Console.WriteLine("{0}, {1}", car.CodeName, car.Location);
```

```c#
var train = new { CodeName = "kerr", Location = 3 };
train = car;
```

If every name and type of the member variable matches, assigning to other object is also possible.