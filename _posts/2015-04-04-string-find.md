---
layout: post
title: "string find"
category: algorithms 
tags: [leetcode,string,find]
---
#### 5.Implement strstr()
Code it now: [https://oj.leetcode.com/problems/implement-strstr/](https://oj.leetcode.com/problems/implement-strstr/) 

###### Question:
Implement strstr(). Returns the index of the first occurrence of needle in haystack, or –1 if needle is not part of haystack.

###### Solution:
**O(nm) runtime, O(1) space – Brute force:**
>The brute force method is straightforward to implement. We scan the needle with the haystack from its first position and start matching all subsequent letters one by one. If one of the letters does not match, we start over again with the next position in the haystack.

``` C++
class Solution {
public:
    int strStr(char *haystack, char *needle) {
        int haystackLen = 0, needleLen = 0;
		while (haystack[haystackLen] != '\0')haystackLen++;
		while (needle[needleLen] != '\0') needleLen++;
      
		for (int i = 0;; ++i)
		{
			for (int j = 0;; ++j)
			{	
			   if (j == needleLen) return i;
				if (i + j == haystackLen) return -1;
				if (needle[j] != haystack[i + j]) break;				
			}
		}
    }
};
```