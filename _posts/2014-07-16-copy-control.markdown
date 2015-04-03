---
layout: post
title: "Copy Control"
date: 2014-07-16 12:46:59 +0800
comments: true
category: C++
tags: [copy control]
---
When we define a class,we specify—explicitly or implicitly—what happens when objects of that class type are copied, moved, assigned, and destroyed. A class controls
these operations by defining five special member functions: **copy constructor**, **copy-assignment operator**, **move constructor**, **move-assignment operator**, and **destructor**. The copy and move constructors define what happens when an object is
initialized from another object of the same type. The copy- and move-assignment operators define what happens when we assign an object of a class type to another
object of that same class type. The destructor defines what happens when an object of the type ceases to exist. Collectively, we’ll refer to these operations as copy
control.

**The Copy Constructor**

A constructor is the copy constructor if its first parameter is a reference to the class type and any additional parameters have default values:

    class Foo {
    public:
    Foo();  // default constructor
    Foo(const Foo&);  // copy constructor
    // ...
    };

**Copy Initialization**

When we use **direct initialization**, we are asking the compiler to use ordinary function matching to select the constructor that best matches the arguments we provide. When we use **copy initialization**, we are asking the compiler to copy the right-hand operand into the object being created, converting that operand if necessary。

Copy initialization happens not only when we define variables using an =, but also when we

- Pass an object as an argument to a parameter of nonreference type
- Return an object from a function that has a nonreference return type
- Brace initialize the elements in an array or the members of an aggregate class

Some class types also use copy initialization for the objects they allocate. For example, the library containers copy initialize their elements when we initialize the container, or when we call an insertor push member. By contrast, elements created by an emplace member are direct initialized.

**Parameters and Return Values**

The fact that the copy constructor is used to initialize nonreference parameters of class type explains why the copy constructor’s own parameter must be a reference. If
that parameter were not a reference, then the call would never succeed—to call the copy constructor, we’d need to use the copy constructor to copy the argument, but to
copy the argument, we’d need to call the copy constructor, and so on indefinitely.

**Introducing Overloaded Assignment**

**Overloaded operators** are functions that have the name operatorfollowed by the symbol for the operator being defined. Hence, the assignment operator is a function
named operator=. Like any other function, an operator function has a return type and a parameter list. The copy-assignment operator takes an argument of the same type as the class：

    class Foo {
    public:
    Foo& operator=(const Foo&); // assignment operator
    // ...
    };

Assignment operators ordinarily should return a reference to their left-hand operand.

**What a Destructor Does**

Just as a constructor has an initialization part and a function body, a destructor has a function body and a destruction part. In a constructor, members are
initialized before the function body is executed, and members are initialized in the same order as they appear in the class. In a destructor, the function body is executed first and then the members are destroyed. Members are destroyed in reverse order from the order in which they were initialized.

The implicit destruction of a member of built-in pointer type does not delete the object to which that pointer points. Members of class type are destroyed by running the member’s own destructor. Unlike ordinary pointers, the smart pointers are class types and have destructors. As a result, unlike ordinary pointers, members that are smart
pointers are automatically destroyed during the destruction phase.

**Classes That Need Destructors Need Copy and Assignment**

If a class needs a destructor, it almost surely also needs the copy-assignment operator and a copy constructor.

**Classes That Need Copy Need Assignment, and Vice Versa**

As an example, consider a class that gives each object its own, unique serial number. Such a class would need a copy constructor to generate a new, distinct serial
number for the object being created. That constructor would copy all the other data members from the given object. This class would also need its own copy-assignment
operator to avoid assigning to the serial number of the left-hand object. However, this class would have no need for a destructor.

**Using = default**

We can explicitly ask the compiler to generate the synthesized versions of the copycontrol members by defining them as = default.

    class Sales_data {
    public:
    // copy control; use defaults
    Sales_data() = default;
    Sales_data(const Sales_data&) = default;
    Sales_data& operator=(const Sales_data &);
    ~Sales_data() = default;
    // other members as before
    };
    Sales_data& Sales_data::operator=(const Sales_data&) =
    default;

**Preventing Copies**

Although most classes should (and generally do) define a copy constructor and a copy-assignment operator, for some classes, there really is no sensible meaning for
these operations. In such cases, the class must be defined so as to prevent copies or assignments from being made. For example, the *iostream* classes prevent copying to
avoid letting multiple objects write to or read from the same IO buffer. It might seem that we could prevent copies by not defining the copy-control members. However, this
strategy doesn’t work: If our class doesn’t define these operations, the compiler will synthesize them.

**Defining a Function as Deleted**

Under the new standard, we can prevent copies by defining the copy constructor and copy-assignment operator as **deleted functions**. A deleted function is one that is
declared but may not be used in any other way. We indicate that we want to define a function as deleted by following its parameter list with = delete:

    struct NoCopy {
    NoCopy() = default;  // use the synthesized default constructor
    NoCopy(const NoCopy&) = delete;  // no copy
    NoCopy &operator=(const NoCopy&) = delete; // no assignment
    ~NoCopy() = default;  // use the synthesized destructor
    // other members
    };

**The Copy-Control Members May Be Synthesized as Deleted**

In essence, the copy-control members are synthesized as deleted when it is impossible to copy, assign, or destroy a member of the class.

**private Copy Control**

Prior to the new standard, classes prevented copies by declaring their copy constructor and copy-assignment operator as *private*.

Classes that want to prevent copying should define their copy constructor and copy-assignment operators using = delete rather than making those members private.

    class PrivateCopy {
    // no access specifier; following members are private by default; see § 7.2 (p.
    268)
    // copy control is private and so is inaccessible to ordinary user code
    PrivateCopy(const PrivateCopy&);
    PrivateCopy &operator=(const PrivateCopy&);
    // other members
    public:
    PrivateCopy() = default; // use the synthesized default constructor
    ~PrivateCopy(); // users can define objects of this type but not copy them
    };

**Assignment Operators**

There are two points to keep in mind when you write an assignment operator:

- Assignment operators must work correctly if an object is assigned to itself.
- Most assignment operators share work with the destructor and copy constructor.

A good pattern to use when you write an assignment operator is to first copy the right-hand operand into a local temporary. Afterthe copy is done, it is safe to destroy the existing members of the left-hand operand. Once the lefthand operand is destroyed, copy the data from the temporary into the members of the left-hand operand.

**Reference Counts**

Reference counting works as follows:

- In addition to initializing the object, each constructor (other than the copy constructor) creates a counter. This counter will keep track of how many objects share state with the object we are creating. When we create an object, there is only one such object, so we initialize the counter to 1.
- The copy constructor does not allocate a new counter; instead, it copies the data members of its given object, including the counter. The copy constructor increments this shared counter, indicating that there is another user of that object’s state.
- The destructor decrements the counter, indicating that there is one less user of the shared state. If the count goes to zero, the destructor deletes that state.
- The copy-assignment operator increments the right-hand operand’s counter and decrements the counter of the left-hand operand. If the counter for the left-hand operand goes to zero, there are no more users. In this case, the copy assignment operator must destroy the state of the left-hand operand.

**Using *swap* in Assignment Operators**

Classes that define swap often use swap to define their assignment operator. These operators use a technique known as **copy and swap**. This technique swaps the lefthand operand with a copy of the right-hand operand:

    // note rhs is passed by value, which means the HasPtr copy constructor
    // copies the string in the right-hand operand into rhs
    HasPtr& HasPtr::operator=(HasPtr rhs)
    {
    // swap the contents of the left-hand operand with the local variable rhs
    swap(*this, rhs); // rhs now points to the memory this object had used
    return *this;  // rhs is destroyed, which deletes the pointer in rhs
    }

**Rvalue References**

To support move operations, the new standard introduced a new kind of reference, an *rvalue reference*. An rvalue reference is a reference that must be bound to an
rvalue. An rvalue reference is obtained by using **&&** rather than **&**. As we’ll see, rvalue references have the important property that they may be bound only to an object that is about to be destroyed. As a result, we are free to “move” resources from an rvalue reference to another object.

**The Library move Function**

Although we cannot directly bind an rvalue reference to an lvalue, we can explicitly cast an lvalue to its corresponding rvalue reference type. We can also obtain an rvalue reference bound to an lvalue by calling a new library function named **move**, which is defined in the *utility* header.

    int i = 42;
    int &r = i;  // ok: r refers to i
    int &&rr = i;// error: cannot bind an rvalue reference to an lvalue
    int &r2 = i * 42;  // error: i * 42 is an rvalue
    const int &r3 = i * 42; // ok: we can bind a reference to const to an rvalue
    int &&rr2 = i * 42;  // ok: bind rr2 to the result of the multiplication
    int &&rr1 = 42;  // ok: literals are rvalues
    int &&rr2 = rr1;  // error: the expression rr1 is an lvalue!
    int &&rr3 = std::move(rr1);  // ok

We can destroy a moved-from object and can assign a new value to it, but we cannot use the value of a moved-from object.

After a move operation, the “moved-from” object must remain a valid, destructible object but users may make no assumptions about its value.

The compiler synthesizes the move constructor and move assignment only if a class does not define any of its own copy-control members and only if all the data members can be moved constructed and move assigned,respectively.

Classes that define a move constructor or move-assignment operator must also define their own copy operations. Otherwise, those members are deleted by default.

**Rvalues Are Moved, Lvalues Are Copied, But Rvalues Are Copied If There Is No Move Constructor.**

**Advice:Updating the Rule of Three**

All five copy-control members should be thought of as a unit: Ordinarily, if a class defines any of these operations, it usually should define them all. As
we’ve seen, some classes must define the copy constructor, copy-assignment operator, and destructor to work correctly. Such classes typically have a resource that the copy members must copy. Ordinarily,copying a resource entails some amount of overhead. Classes that define the
move constructor and move-assignment operator can avoid this overhead in those circumstances where a copy isn’t necessary.

**Rvalue References and Member Functions**

Overloaded functions that distinguish between moving and copying a parameter typically have one version that takes a *const T&* and one that takes a *T&&*.

    class StrVec {
    public:
    void push_back(const std::string&);  // copy the element
    void push_back(std::string&&);  // move the element
    // other members as before
    };
    // unchanged from the original version in § 13.5 (p. 527)
    void StrVec::push_back(const string& s)
    {
    chk_n_alloc(); // ensure that there is room for another element
    // construct a copy of s in the element to which first_free points
    alloc.construct(first_free++, s);
    }
    void StrVec::push_back(string &&s)
    {
    chk_n_alloc(); // reallocates the StrVec if necessary
    alloc.construct(first_free++, std::move(s));
    }

**reference qualifier**

Symbol used to indicate that a nonstatic member function
can be called on an lvalue or an rvalue. The qualifier, &or &&, follows the parameter list or the const qualifier if there is one. A function qualified by & may be called only on lvalues; a function qualified by && may be called only on rvalues.

**rvalue reference**

Reference to an object that is about to be destroyed.