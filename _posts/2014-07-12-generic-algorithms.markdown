---
layout: post
title: "Generic Algorithms"
date: 2014-07-12 20:07:54 +0800
comments: true
category: C++
tags: [generic]
---
> - Iterators Make the Algorithms Container Independent, But Algorithms Do Depend on Element-Type Operations
> - Algorithms Never Execute Container Operations
> - Ordinarily it is best to use cbegin() and cend() with
algorithms that read, but do not write, the elements. However, if you plan to use the iterator returned by the algorithm to change an element’s value, then you need to pass begin()and end().
> - Algorithms that take a single iterator denoting a second sequence assume that the second sequence is at least as large at the first.
> - Algorithms that write to a destination iterator assumethe destination is large enough to hold the number of elements being written.
> - The library algorithms operate on iterators, not containers. Therefore, an algorithm cannot (directly) add or remove elements.
> - Lambdas with function bodies that contain anything other than a single return statement that do not specify a return type return void.
> - A lambda may use a variable local to its surrounding function only if the lambda captures that variable in its capture list.
> - The capture list is used for local nonstatic variables only; lambdas can use local statics and variables declared outside the function directly.

**Read-Only Algorithms**

**find**

    string val = "a value";  // value we'll look for
    // this call to find looks through string elements in a list
    auto result = find(1st.cbegin(), 1st.cend(), val);

**accumulate**(defined in the numeric header)
    
    // sum the elements in vec starting the summation with the value 0
    int sum = accumulate(vec.cbegin(), vec.cend(), 0);
    stringsum = accumulate(v.cbegin(), v.cend(), string(""));

**equal**

    // roster2 should have at least as many elements as roster1
    equal(roster1.cbegin(), roster1.cend(), roster2.cbegin());

**Algorithms That Write Container Elements**

**fill**

It is a fairly common beginner mistake to call fill_n(or similar algorithms that write to elements) on a container that has no elements.

    fill(vec.begin(),vec.end(), 0);  // reset each element to 0
    fill_n(dest,n, val)

**back_inserter**(defined in the iterator header)

One way to ensure that an algorithm has enough elements to hold the output is to use an *insert iterator*. An insert iterator is an iterator that adds elements to a container.

*back_inserter* takes a reference to a container and returns an insert iterator bound to that container. When we assign through that iterator, the assignment calls
push_back to add an element with the given value to the container.

    vector<int>vec; // empty vector
    // ok: back_inserter creates an insert iterator that adds elements to vec
    fill_n(back_inserter(vec), 10, 0);  // appends ten elements to vec

**copy**

    int a1[] = {0,1,2,3,4,5,6,7,8,9};
    int a2[sizeof(a1)/sizeof(*a1)];  // a2 has the same size as a1
    // ret points just past the last element copied into a2
    auto ret = copy(begin(a1), end(a1), a2);  // copy a1 into a2

**replace**

    // replace any element with the value 0 with 42
    replace(ilst.begin(), ilst.end(), 0, 42);
	// use back_inserter to grow destination as needed
	replace_copy(ilst.cbegin(), ilst.cend(),
	back_inserter(ivec), 0, 42);

**Algorithms That Reorder Container Elements**

**sort**(using the element types's < operator)

**Eliminating Duplicates**

To eliminate the duplicated words, we will first *sort* the vector so that duplicated words appear adjacent to each other. Once the vector is sorted, we can use another
library algorithm, named unique, to reorder the vector so that the *unique* elements appear in the first part of the vector. Because algorithms cannot do container operations, we’ll use the *erase* member of vector to actually remove the elements.
    
    void elimDups(vector<string> &words)
    {
    // sort words alphabetically so we can find the duplicates
    sort(words.begin(), words.end());
    // unique  reorders the input range so that each word appears once in the
    // front portion of the range and returns an iterator one past the unique range
    auto end_unique = unique(words.begin(), words.end());
    // erase uses a vector operation to remove the nonunique elements
    words.erase(end_unique, words.end());
    }

**Customizing Operations**

the sort algorithm uses the element type’s < operator. However, we might want to sort a sequence into a different order from that defined by <, or our sequence might have elements of a type (such as Sales_data) that does not have a < operator. In both cases, we need to override the default behavior of *sort*.

**Passing a Function to an Algorithm**

**predicate**

A predicate is an expression that can be called and that returns a value that can be used as a condition. The predicates used by library algorithms are either unary
predicates(meaning they have a single parameter) or binary predicates(meaning they have two parameters). The algorithms that take predicates call the given
predicate on the elements in the input range. As a result, it must be possible to convert the element type to the parameter type of the predicate.

    // comparison function to be used to sort by word length
    bool isShorter(const string &s1, const string &s2)
    {
    return s1.size() < s2.size();
    }
    // sort on word length, shortest to longest
    sort(words.begin(), words.end(), isShorter);

**stable_sort**(maintains the original order among equal elements.)

    // resort by length, maintaining alphabetical order among words of the same length
    stable_sort(words.begin(), words.end(), isShorter);

**Lambda Expressions**

**callable object**

We can pass any kind of *callable object* to an algorithm. An object or expression is callable if we can apply the call operator to it. That is, if e is a callable expression, we can write e(args) where args is a comma-separated list of zero or more arguments.

The only callables we’ve used so far are functions and function pointers. There are two other kinds of callables: classes that overload the function-call operator, and lambda expressions.

**lambda expressions**
A lambda expression represents a callable unit of code. It can be thought of as an unnamed, inline function. Like any function, a lambda has a return type, a parameter list, and a function body. Unlike a function, lambdas may be defined inside a function. A lamba expression has the form

***[capturelist] (parameter list) -> return type  { function body}***

where capture listis an (often empty) list of local variables defined in the enclosing function; return type, parameter list, and function body are the same as in any
ordinary function. However, unlike ordinary functions, a lambda must use a trailing return to specify its return type. We can omit either or both of the parameter list and return type but must always include the capture list and function body.

    // sort words by size, but maintain alphabetical order for words of the same size
    stable_sort(words.begin(), words.end(),
    [](const string &a, const string &b)
    { return a.size() < b.size();});

**find_if**

    // get an iterator to the first element whose size() is >= sz
    auto wc = find_if(words.begin(), words.end(),
    [sz](const string &a)
    { return a.size() >= sz; });

**for_each**
    
    // print words of the given size or longer, each one followed by a space
    for_each(wc, words.end(),
    [](const string &s){cout << s << " ";});
    cout << endl;

**Lambda Captures and Returns**

When we define a lambda, the compiler generates a new (unnamed) class type that corresponds to that lambda. By default, the class generated from a lambda contains a data member corresponding to the variables captured by the lambda. Like the data members of any class, the data members of a lambda are initialized when a lambda object is created. Similar to parameter passing, we can capture variables by value or by reference.

**Mutable Lambdas**

By default, a lambda may not change the value of a variable that it copies by value. If we want to be able to change the value of a captured variable, we must follow the parameter list with the keyword mutable. Lambdas that are mutable may not omit the parameter list.

**Specifying the Lambda Return Type**

When we need to define a return type for a lambda, we must use a trailing return type.

    transform(vi.begin(),vi.end(), vi.begin(),
    [](int i) -> int
    { if (i < 0) return -i; else return i; });

**The Library bind Function**( defined in the *functional* header)

The bind function can be thought of as a general-purpose function adaptor.It takes a callable object and generates a new callable that “adapts” the parameter list of the original object.The general form of a call to bind is:

***auto newCallable= bind(callable, arg_list);***

where newCallable is itself a callable object and arg_list is a comma-separated list of arguments that correspond to the parameters of the given callable. That is, when we
call newCallable, newCallable calls callable, passing the arguments in arg_list.

The arguments in arg_list  may include names of the form _n, where n is an integer.These arguments are “placeholders” representing the parameters of newCallable. They stand “in place of” the arguments that will be passed to newCallable. The number n is the position of the parameter in the generated callable: _1 is the first parameter in newCallable, _2 is the second, and so forth.The _n names are defined in a namespace named placeholders. That namespace is itself defined inside the std namespace.

    bool check_size(const string &s, string::size_type sz)
    {
    return s.size() >= sz;
    }
    // check6 is a callable object that takes one argument ``of type string
    // and calls check_size on its given string and the value 6
    auto check6 = bind(check_size, _1, 6);
    string s = "hello";
    bool b1 = check6(s);
    auto wc = find_if(words.begin(), words.end(),
    bind(check_size, _1, sz));

