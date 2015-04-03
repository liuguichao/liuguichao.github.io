---
layout: post
title: "const member functions"
date: 2014-07-09 15:31:26 +0800
comments: true
category: C++
tags: [const]
---
#### Introducing const Member Functions 
```c++
std::string isbn() const
 { return bookNo; }
```

The other important part about the isbn function is the keyword *const* that follows the parameter list. The purpose of that *const* is to modify the type of the implicit this pointer.

By default, the type of *this* is a *const* pointer to the *nonconst* version of the class type. We cannot call an ordinary member function on a *const* object.

However, *this* is implicit and does not appear in the parameter list. There is no place to indicate that *this* should be a pointer to *const*. The language resolves this
problem by letting us put *const* after the parameter list of a member function. A *const* following the parameter list indicates that *this* is a pointer to *const*. Member
functions that use *const* in this way are const member functions.

The fact that *this* is a pointer to *const* means that *const* member functions cannot change the object on which they are called. Thus, isbn may read but not
write to the data members of the objects on which it is called.

**Note**

Objects that are *const*, and references or pointers to *const* objects, may call only *const* member functions.