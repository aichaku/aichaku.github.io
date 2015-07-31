---
layout: post
title: The Longest Palindrome Subsequence
---

# {{ page.title }}

'''
#include <stdio.h>
#include <string.h>

int max(int a, int b) {
	if (a > b) return a;
	else return b;
}

int lps(char* str, int i, int j)
{
	if (str == NULL) return 0;
	if (i == j) return 1;

	if (str[i] == str[j]) {
		return lps(str, i + 1, j - 1) + 2;
	}
	else return max(lps(str, i, j - 1), lps(str, i + 1, j));
}

int main()
{
	char str[] = "character";
	printf("the longest palindrome subsequence is {%s} [%d]", "N/A", lps(str, 0, strlen(str)));
}
'''
