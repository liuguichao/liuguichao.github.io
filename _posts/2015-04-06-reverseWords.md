---
layout: post
title: "Reverse Words in a String"
category: algorithms
tags: [leetcode,string,reverse]
---
#### 6. Reverse Words in a String
Code it now: [https://oj.leetcode.com/problems/reverse-words-in-a-string/](https://oj.leetcode.com/problems/reverse-words-in-a-string/)

###### Question:
Given an input string s, reverse the string word by word.
For example, given s = "the sky is blue", return "blue is sky the".

###### Solution:

**O(n) runtime, O(n) space:**

``` C++
class Solution {
public:
    void reverseWords(string &s) {
    string str;
	int j=s.length();
	for(int i=s.length()-1;i>=0;i--)
	{
		if(s[i]==' ')
		{
			j=i;
		}
		else if(i==0||s[i-1]==' ')
		{
			if(str.length()>0)
				str.append(" ");

			str.append(s.substr(i,j-i));
		}
	}
	s=str;
    }
};
```
#### 7. Reverse Words in a String II
###### Question:
Similar to Question [6. Reverse Words in a String], but with the following constraints:
“The input string does not contain leading or trailing spaces and the words are always
separated by a single space.”

###### Solution:
**O(n) runtime, O(1) space – In-place reverse:**

``` C++
void reverse(string& s,int begin,int end)
{
	char temp;
	for(int i=0;i<(end-begin)/2;++i)
	{
		temp=s[begin+i];
		s[begin+i]=s[end-i-1];
		s[end-i-1]=temp;
	}
}
void reverseWords(string &s)
{
	reverse(s,0,s.length());
	int i=0;
	for(int j=0;j<=s.length();++j)
	{
		if(s[j]==' '||j==s.length())
		{
			reverse(s,i,j);
			i=j+1;
		}
	}
}
```