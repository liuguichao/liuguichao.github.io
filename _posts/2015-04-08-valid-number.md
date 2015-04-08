---
layout: post
title: "Valid Number"
category: algorithms 
tags: [leetcode,string,atoi,valid]
---
#### 8. String to Integer (atoi)
Code it now: [https://oj.leetcode.com/problems/string-to-integer-atoi/](https://oj.leetcode.com/problems/string-to-integer-atoi/)
###### Question:
Implement atoi to convert a string to an integer.
The atoi function first discards as many whitespace characters as necessary until the first non-whitespace character is found. Then, starting from this character, takes an optional initial plus or minus sign followed by as many numerical digits as possible, and interprets them as a numerical value.

The string can contain additional characters after those that form the integral number,which are ignored and have no effect on the behavior of this function.

If the first sequence of non-whitespace characters in str is not a valid integral number, or if no such sequence exists because either str is empty or it contains only whitespace characters, no conversion is performed.
If no valid conversion could be performed, a zero value is returned. If the correct value is out of the range of representable values, the maximum integer value (2147483647) or the
minimum integer value (–2147483648) is returned.

###### Solution:
``` C++
class Solution {
public:
	const static int maxDiv10=INT_MAX/10;
    int atoi(string str) {
        int i=0,j=str.length();
        while(i<j&&isblank(str.at(i))) i++;
         if(i==j) return 0;
        int sign=1;
        if(str.at(i)=='+')
        	i++;
        else if(str.at(i)=='-')
        {
        	sign=-1;
        	i++;
        }
        int num=0;
        while(i<j&&isdigit(str.at(i)))
        {
        	int digit=str.at(i)-'0';
        	if(num>maxDiv10||num==maxDiv10&&digit>=8)
        		return sign==1?INT_MAX:INT_MIN;
        	num=num*10+digit;
        	++i;
        }
        return sign*num;

    }
};
```
#### 9. Valid Number
Code it now: [https://oj.leetcode.com/problems/valid-number/](https://oj.leetcode.com/problems/valid-number/)

######Question:
Validate if a given string is numeric.
###### Solution:
``` C++
class Solution {
public:
    bool isNumber(string s) {
        int i=0,j=s.length();
        while(i<j&&isblank(s.at(i))) i++;
        if(i<j&&(s.at(i)=='+'||s.at(i)=='-')) i++;
        bool isNumeric=false;
        while(i<j&&isdigit(s.at(i)))
        {
        	i++;
        	isNumeric=true;
        }
        if(i<j&&s.at(i)=='.')
        {
        	i++;
        	 while(i<j&&isdigit(s.at(i)))
        	 {
        	    i++;
        	    isNumeric=true;
       		 }
        }
        if(isNumeric&&i<j&&s.at(i)=='e')
        {
        	i++;
        	isNumeric=false;
        	if(i<j&&(s.at(i)=='+'||s.at(i)=='-')) i++;
        	while(i<j&&isdigit(s.at(i)))
        	{
        		i++;
        		isNumeric=true;
        	}
        }
        while(i<j&&isblank(s.at(i))) i++;
        return isNumeric&&i==j;
    }
};
```