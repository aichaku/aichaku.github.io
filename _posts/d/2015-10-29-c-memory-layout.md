---
layout: post
title: C Memory Layout
category: d
tags: [C, memory layout]
---

The most confusing thing in memory layout in C is the difference between BSS and Data section. Bear this in mind. BSS is the region for uninitialized global variables, and it will be set to 0 while kernel allocate the area. Of course, Data section is for initialized global and static variables. For more information, please visit [here][1] and read the explanation.

<img class="post-img" src="http://cs-fundamentals.com/images/code-data-segments.png" />

### Test code to see where the areas take place

```c
#include <iostream>
#include <stdio.h>

using namespace std;

const char rodata[] = "abcdefg";
int bss1;
int bss2;
int data = 0;

int main()
{
	cout << "data region starts from " << &rodata << endl;
	cout << "data region " << &data << endl;
	cout << "text segment starts from " << main << endl;


	int stack1;
	int stack2;
	int stack3;

	static int bss3;

	cout << "stack starts from " << &stack1 << endl;
	cout << "stack " << &stack2 << endl;
	cout << "stack " << &stack3 << endl;

	cout << "bss area starts from " << &bss1 << endl;
	cout << "bss area " << &bss2 << endl;
	cout << "bss area " << &bss3 << endl;
}
```

###
To read more, visit [here][2]

[1]: http://www.geeksforgeeks.org/memory-layout-of-c-program/
[2]: http://www.ualberta.ca/CNS/RESEARCH/LinuxClusters/mem.html