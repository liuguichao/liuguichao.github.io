---
layout: post
title: "longest substring"
category: algorithms
tags: [leetcode,substring]
---
#### 10. Longest Substring Without Repeating Characters
Code it now:[https://oj.leetcode.com/problems/longest-substring-without-repeating-characters/](https://oj.leetcode.com/problems/longest-substring-without-repeating-characters/)

###### Question:
Given a string, find the length of the longest substring without repeating characters. For example, the longest substring without repeating letters for “abcabcbb” is “abc”, which
the length is 3. For “bbbbb” the longest substring is “b”, with the length of 1.

###### Solution:
**O(n) runtime, O(1) space**

``` C++
int lengthOfLongestSubstring(string s) {
        int index[256];
        for(auto& i:index)
        	i=-1;
        int i=0,maxLen=0;
        for(int j=0;j<s.length();++j)
        {
        	if(index[s.at(j)]>=i)
        	{
        		i=index[s.at(j)]+1;        	
        	}
        	index[s.at(j)]=j;
        	maxLen=max(j-i+1,maxLen);
        }
        return maxLen;
    }
```
#### 11. Longest Substring with At Most Two Distinct Characters

###### Question:
Given a string S, find the length of the longest substring T that contains at most two distinct characters.

For example,Given S = “eceba”,T is "ece" which its length is 3.
###### Solution:
``` C++
int lengthOfLongestSubStringTwoDistinct(string s)
{
    int count[256];
    for(auto &i:count)
        i=0;
    int i=0,numDistinct=0,maxLen=0;
    for(int j=0;j!=s.length();++j)
    {
       if(count[s.at(j)]==0) numDistinct++;
       count[s.at(j)]++;
       while(numDistinct>2)
       {
            count[s.at(i)]--;
            if(count[s.at(i)]==0) numDistinct--;
            i++;
       }
       maxLen=max(j-i+1,maxLen);
    }   
    return maxLen;
}
```