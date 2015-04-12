---
layout: post
title: "Longest Palindromic Substring"
category: algorithms
tags: [leetcode,palindromic,distance]
---
#### 13. Longest Palindromic Substring
Code it now: [https://oj.leetcode.com/problems/longest-palindromic-substring/](https://oj.leetcode.com/problems/longest-palindromic-substring/)

###### Question:
Given a string S, find the longest palindromic substring in S. You may assume that the maximum length of S is 1000, and there exists one unique longest palindromic substring.
###### Hint:
First, make sure you understand what a palindrome means. A palindrome is a string which reads the same in both directions. For example, “aba” is a palindome, “abc” is not.

###### Solution:
**O(n^2) runtime, O(1) space**
>We observe that a palindrome mirrors around its center. Therefore, a palindrome can be expanded from its center, and there are only 2n – 1 such centers.

>You might be asking why there are 2n – 1 but not n centers? The reason is the center of a palindrome can be in between two letters. Such palindromes have even number of letters (such as “abba”) and its center are between the two ‘b’s.

>Since expanding a palindrome around its center could take O(n) time, the overall complexity is O(n^2).

``` C++
class Solution {
public:
    int expandAroundCenter(string &s,int left,int right)
    {
        int L=left,R=right;
        while(L!=-1&&R!=s.length()&&s[L]==s[R])
        {
            L--;
            R++;
        }
        return R-L-1;
    }
    string longestPalindrome(string s) {
       int start=0,end=0;
       for(int i=0;i!=s.length();++i)
       {
         int len1=expandAroundCenter(s,i,i);
         int len2=expandAroundCenter(s,i,i+1);
         int len=max(len1,len2);
         if(len>end-start)
         {
            start=i-(len-1)/2;
            end=i+len/2;
         }
       }
       return s.substr(start,end-start+1);
    }
};
```
**O(n) runtime, O(n) space – Manacher’s algorithm**

#### 14. One Edit Distance
###### Question:
Given two strings S and T, determine if they are both one edit distance apart.
###### Hint:
>1. If | n – m | is greater than 1, we know immediately both are not one-edit distance
apart.
>2. It might help if you consider these cases separately, m == n and m ≠ n.
>3. Assume that m is always ≤ n, which greatly simplifies the conditional statements.
If m > n, we could just simply swap S and T.
>4. If m == n, it becomes finding if there is exactly one modified operation. If m ≠ n,
you do not have to consider the delete operation. Just consider the insert operation
in T.

###### Solution:
** O(n) runtime, O(1) space – Simple one-pass: **

``` C++
bool isOneEditDistance(string s,string t)
{
    int m=s.length(),n=t.length();
    if(m>n) return isOneEditDistance(t,s);
    if(n-m>1) return false;
    int i=0,shift=n-m;
    while(i!=m&&s[i]==t[i]) i++;
    if(i==m) return true;
    else
    {
        if(shift==0) i++;
        while(i!=m&&s[i]==t[i+shift]) i++;
        return i==m;
    }
}
```