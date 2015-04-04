---
layout: post
title: "Templates and Generic Programming"
date: 2014-07-20 10:05:00 +0800
comments: true
category: C++
tags: [templates]
---
Both object-oriented programming (OOP) and generic programming deal with types that are not known at the time the program is written. The distinction between the two is that OOP deals with types that are not known until **run time**, whereas in generic programming the types become known during **compilation**.

** Function Templates **

Rather than defining a new function for each type, we can define a function template. A function template is a formula from which we can generate type-specific versions of that function. 

``` C++
template <typename T>
int compare(const T &v1, const T &v2)
{
if (v1 < v2) return -1;
if (v2 < v1) return 1;
return 0;
}

template <typename T> 
int compare(const T &v1, const T &v2)
{
if (less<T>()(v1, v2)) return -1;
if (less<T>()(v2, v1)) return 1;
return 0;
}
```
At emplate definition starts with the keyword **template** followed by a **template parameter list**, which is a comma-separated list of one or more template parameters bracketed by the less-than (<) and greater-than (>) tokens.

The compiler uses the deduced template parameter(s) to **instantiate** a specific version of the function for us. When the compiler instantiates a template, it creates a new “instance” of the template using the actual template argument(s) in place of the corresponding template parameter(s). 

** Template Compilation **

When the compiler sees the definition of a template, it does not generate code. It generates code only when we instantiate a specific instance of the template. The fact that code is generated only when we use a template (and not when we define it) affects how we organize our source code and when errors are detected.

Definitions of function templates and member functions of class templates are ordinarily put into header files.Users of the template must include the header for the template and for any types used to instantiate that template.

** Template Type Aliases **

Because a template is not a type, we cannot define a typedef that refers to a template. However, the new standard lets us define a type alias for a class template.

``` C++
template <typenameT> using twin = pair<T, T>;
twin<string> authors; // authors is a pair<string, string>

template <typename T> using partNo = pair<T, unsigned>;
partNo<string> books;  // books is a pair<string, unsigned>
partNo<Vehicle> cars;  // cars is a pair<Vehicle, unsigned>
partNo<Student> kids;  // kids is a pair<Student, unsigned>
```
** Using Class Members That Are Types **

By default, the language assumes that a name accessed through the scope operator is not a type. As a result, if we want to use a type member of a template type parameter, we must explicitly tell the compiler that the name is a type. We do so by using the keyword **typename**.

``` C++
template <typename T>
typename T::value_type top(const T& c)
{
if (!c.empty())
return c.back();
else
return typename T::value_type();
}
```
**To be continue……**