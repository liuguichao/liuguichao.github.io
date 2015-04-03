---
layout: post
title: "Associative Containers"
date: 2014-07-13 20:40:14 +0800
comments: true
category: C++
tags: [Containers]
---
Associative and sequential containers differ from one another in a fundamental way: Elements in an associative container are stored and retrieved by a key. In contrast,
elements in a sequential container are stored and accessed sequentially by their position in the container.

The *map* and *multimap* types are defined in the map header; the *set* and *multiset* types are in the set header; and the *unordered* containers are in the
unordered_map and unordered_set headers.

**map**

    // count the number of times each word occurs in the input
    map<string, size_t> word_count; // empty map from string to size_t
    string word;
    while (cin >> word)
    ++word_count[word];  // fetch and increment the counter for word
    for (const auto &w : word_count) // for each element in the map
    // print the results
    cout <<  w.first << " occurs " << w.second
    << ((w.second > 1) ? " times" : " time") << endl;

**set**

    // count the number of times each word occurs in the input
    map<string, size_t> word_count; // empty map from string to size_t
    set<string> exclude = {"The", "But", "And", "Or", "An", "A",
    "the", "but", "and", "or", "an",
    "a"};
    string word;
    while (cin >> word)
    // count only words that are not in exclude
    if (exclude.find(word) == exclude.end())
    ++word_count[word];  // fetch and increment the counter for word

**Requirements on Key Type**

For the ordered containers—map, multimap, set, and multiset—the key type must define a way to compare the elements. By default, the library uses the < operator for
the key type to compare the keys. 

**Key Types for Ordered Containers**（strict weak ordering）

In practice, what’s important is that a type that defines a < operator that “behaves normally” can be used as a key.

**The pair Type**（defined in the utility header）

    pair<map<string,size_t>::iterator, bool> ret =
    word_count.insert(make_pair(word, 1));

**Subscripting a map**

Subscripting a mapbehaves quite differently from subscripting an array or vector:Using a key that is not already present adds an element with that key to the map. sometimes we only want to know whether an element is present and do not want to add the element if it is not.
In such cases, we must not use the subscript operator.

when we subscript a map, we get a mapped_type object; when we dereference a map iterator,we get a value_type object.

**Accessing Elements**

![](http://i.imgur.com/3rxxwMc.jpg)


Using *find* Instead of Subscript for maps.

    if(word_count.find("foobar") == word_count.end())
    cout << "foobar is not in the map" << endl;

	string search_item("Alain de Botton"); // author we'll look for
	auto entries = authors.count(search_item); // number of elements
	auto iter = authors.find(search_item); // first entry for this author
	// loop through the number of entries there are for this author
	while(entries) {
	cout << iter->second << endl; // print each title
	++iter;  // advance to the next title
	--entries;  // keep track of how many we've printed
	}


We are guaranteed that iterating across a multimap or multiset returns all the elements with a given key in sequence.

The iterator returned from lower\_bound may or may not refer to an element with the given key. If the key is not in the container, then lower\_bound refers to the first point at which this key can be inserted while preserving the element order within the container.

    // definitions of authors and search_item as above
    // beg and end denote the range of elements for this author
    for (auto beg = authors.lower_bound(search_item),
    end = authors.upper_bound(search_item);
    beg != end; ++beg)
    cout << beg->second << endl; // print each title

If lower\_bound and upper\_bound return the same iterator, then the given key is not in the container.

**The equal_range Function**

    // definitions of authors and search_item as above
    // pos holds iterators that denote the range of elements for this key
    for (auto pos = authors.equal_range(search_item);
    pos.first != pos.second; ++pos.first)
    cout << pos.first->second << endl; // print each title

**The Unordered Containers**

The new standard defines four unordered associative containers. Rather than using a comparison operation to organize their elements, these containers use a hash
function and the key type’s ==operator. An unordered container is most useful when we have a key type for which there is no obvious ordering relationship among the
elements. These containers are also useful for applications in which the cost of maintaining the elements in order is prohibitive.

**Managing the Buckets**

The unordered containers are organized as a collection of buckets, each of which holds zero or more elements. These containers use a hash function to map elements to buckets. To access an element, the container first computes the element’s hash code, which tells which bucket to search. The container puts all of its elements with a given hash value into the same bucket. If the container allows multiple elements with a given key, all the elements with the same key will be in the same bucket. As a result, the performance of an unordered container depends on the quality of its hash function and on the number and size of its buckets.

The hash function must always yield the same result when called with the same argument. Ideally, the hash function also maps each particular value to a unique bucket. However, a hash function is allowed to map elements with differing keys to the same bucket. When a bucket holds several elements, those elements are searched sequentially to find the one we want. Typically, computing an element’s hash code and finding its bucket is a fast operation. However, if the bucket has many elements, many comparisons may be needed to find a particular element.

**Requirements on Key Type for Unordered Containers**

By default, the unordered containers use the ==operator on the key type to compare elements. They also use an object of type hash<key\_type> to generate the hash
code for each element. The library supplies versions of the hash template for the builtin types, including pointers. It also defines hash for some of the library types, including strings and the smart pointer types. Thus, we can directly define unordered containers whose key is one of the built-in types (including pointer types), or a string, or a smart pointer.

However,we cannot directly define an unordered container that uses a our own class types for its key type. Unlike the containers, we cannot use the hash template directly. Instead, we must supply our own version of the hash template.


