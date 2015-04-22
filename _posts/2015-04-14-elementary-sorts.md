---
layout: post
title: "Elementary Sorts"
category: algorithms
tags: [sort]
---
#### Selection sort
One of the simplest sorting algorithms works as follows: First, find the smallest item in the array and exchange it with the first entry (itself if the first entry is already the smallest). Then, find the next smallest item and exchange it with the second entry. Continue in this way until the entire array is sorted. This method is called
selection sort because it works by repeatedly selecting the smallest remaining item.

>Proposition A. Selection sort uses ~ N^2/2 compares and N exchanges to sort an array of length N.

>Proof: For each i from 0 to N - 1, there is one exchange and (N - 1 - i) compares, so the totals are N exchanges and (N - 1) + (N - 2) + . . . + 2 + 1+ 0 = N(N - 1) / 2 ~ N^2 / 2 compares.

###### Two signature properties:

**1. Running time is insensitive to input.**

it takes about as long to run selection sort for an array that is already in order or for an array
with all keys equal as it does for a randomly-ordered array!

**2. Data movement is minimal.**

Each of the N exchanges changes the value of two array entries, so selection sort uses N exchanges—the number of array accesses is a linear function of the array size. None of the other sorting algorithms that we consider have
this property (most involve linearithmic or quadratic growth). 

``` C++
template<class Item>
void exch(Item &a, Item &b)
{
	Item t=a;a=b;b=t;
}

template<class Item>
void selection(Item a[],int l,int r)
{	
	for(int i=l;i<r;++i)
	{
		int min=i;
		for(int j=i+1;j<=r;++j)
		{
			if(a[j]<a[min]) min=j;
		}
		exch(a[i],a[min]);
	}
}
```
#### Insertion sort
The algorithm that people often use to sort bridge hands is to consider the cards one at a time, inserting each into its proper place among those already considered (keeping them sorted). In a computer implementation, we need to make
space to insert the current item by moving larger items one position to the right, before inserting the current item into the vacated position.

As in selection sort, the items to the left of the current index are in sorted order during the sort, but they are not in their final position, as they may have to be moved to make room for smaller items encountered later. The array is, however, fully sorted when the index reaches the right end.

Unlike that of selection sort, the running time of insertion sort depends on the initial order of the items in the input. For example, if the array is large and its entries are already in order (or nearly in order), then insertion sort is much, much faster than if the entries are randomly ordered or in reverse order.

>Proposition B. Insertion sort uses ~ N^2/4 compares and ~ N^2/4 exchanges to sort
a randomly ordered array of length N with distinct keys, on the average. The worst
case is ~ N^2/2 compares and ~ N^2/2 exchanges and the best case is N - 1 compares
and 0 exchanges.

**Insertion sort works well for certain types of nonrandom arrays that often arise in
practice, even if they are huge.**

``` C++
template<class Item>
void Insertion(Item a[],int l,int r)
{
	for(int i=l+1;i<=r;++i)
	{
		for(int j=i;j>0&&a[j]<a[j-1];j--)
		{			
			exch(a[j],a[j-1]);
		}
	}
}
``` 
#### Shellsort
Insertion sort is slow for large unordered arrays because the only exchanges it does involve adjacent entries, so items can move through the array only one place at a time. Shellsort is a simple extension of insertion sort that gains speed by allowing exchanges of array entries that are far apart, to produce partially sorted arrays that can be efficiently sorted, eventually by insertion sort.

The idea is to rearrange the array to give it the property that taking every hth entry
(starting anywhere) yields a sorted subsequence. Such an array is said to beh-sorted. Put
another way, an h-sorted array is h independent sorted subsequences, interleaved
together. By h-sorting for some large values of h, we can move items in the array
long distances and thus make it easier to h-sort for smaller values of h. Using such
a procedure for any sequence of values of h that ends in 1 will produce a sorted array: that is shellsort. 

>Property E. The number of compares used by shellsort with the increments 1, 4,
13, 40, 121, 364, . . . is bounded by a small multiple of N times the number of increments used.

``` C++
template<class Item>
void ShellSort(Item a[],int l,int r)
{
	int h=1;
	while(h<=(r-l)/3) h=h*3+1;
	while(h>=1)
	{
		for(int i=l+h;i<=r;++i)
		{			
			for(int j=i;j>=h&&a[j]<a[j-h];j-=h)
				exch(a[j],a[j-h]);
		}		
		h/=3;
	}
}
```