---
layout: post
title: "Missing Ranges"
category: algorithms
tags: [leetcode,missing ranges]
---
#### 12. Missing Ranges
###### Question:
Given a sorted integer array where the range of elements are [0, 99] inclusive, return its missing ranges.

For example, given [0, 1, 3, 50, 75], return [“2”, “4->49”, “51->74”, “76->99”]

###### Solution:

``` C++
string makeRange(int i,int j)
{
   return i==j?to_string(i):to_string(i)+"->"+to_string(j);
}
vector<string> findMissingRanges(vector<int> vals,int start,int end)
{
    vector<string> ranges;
    int prev=start-1;
    for(int i=0;i<=vals.size();++i)
    {
        int curr=(i==vals.size()?end:vals[i]);
        if(curr-prev>=2)
            ranges.push_back(makeRange(prev+1,curr-1));
        prev=curr;
    }
    return ranges;
}
```