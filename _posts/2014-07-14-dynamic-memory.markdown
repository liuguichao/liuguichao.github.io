---
layout: post
title: "Dynamic Memory"
date: 2014-07-14 13:06:30 +0800
comments: true
category: C++
tags: [pointer]
---

**smart pointer**

To make using dynamic memory easier (and safer), the new library provides two smart pointer types that manage dynamic objects. A smart pointer acts like a regular pointer with the important exception that it automatically deletes the object to which it points. The new library defines two kinds of smart pointers that differ in how
they manage their underlying pointers: *shared\_ptr*, which allows multiple pointers to refer to the same object, and *unique\_ptr*, which “owns” the object to which it points.
The library also defines a companion class named *weak\_ptr* that is a weak reference to an object managed by a shared_ptr. All three are defined in the memory header.

**The *shared_ptr* Class**

Like vectors, smart pointers are templates. Therefore, when we create a smart pointer, we must supply additional information—in this case, the type to which the pointer can point. As with vector, we supply that type inside angle brackets that follow the name of the kind of smart pointer we are defining.

    shared_ptr<string>p1;  // shared_ptr that can point at a string
    shared_ptr<list<int>> p2; // shared_ptr that can point at a list of ints
    
    // if p1 is not null, check whether it's the empty string
    if (p1 && p1->empty())
    *p1 = "hi";  // if so, dereference p1 to assign a new value to that string

![](http://i.imgur.com/jrVh8xa.jpg)

**The make_shared Function**

The safest way to allocate and use dynamic memory is to call a library function named ***make_shared***. This function allocates and initializes an object in dynamic memory and returns a shared_ptr that points to that object. Like the smart pointers,make_shared is defined in the memory header.

    // shared_ptr that points to an int with value 42
    shared_ptr<int> p3 = make_shared<int>(42);
    // p4 points to a string with value 9999999999
    shared_ptr<string> p4 = make_shared<string>(10, '9');
    // p5 points to an int that is value initialized (§ 3.3.1 (p. 98)) to 0
    shared_ptr<int> p5 = make_shared<int>();
    // p6 points to a dynamically allocated, empty vector<string>
    auto p6 = make_shared<vector<string>>();

**Copying and Assigning shared_ptrs**

When we copy or assign a shared_ptr, each shared_ptr keeps track of how many other shared_ptrs point to the same object.

We can think of a shared_ptr as if it has an **associated counter**, usually referred to as a **reference count**. Whenever we copy a shared_ptr, the count is incremented.
For example, the counter associated with a shared_ptr is incremented when we use it to initialize another shared_ptr, when we use it as the right-hand operand of an
assignment, or when we pass it to or return it from a function by value . The counter is decremented when we assign a new value to the shared_ptrand when the shared_ptr itself is destroyed, such as when a local
shared_ptr goes out of scope.Once a shared_ptr’s counter goes to zero, the shared_ptr automatically frees the object that it manages.

    auto p = make_shared<int>(42); // object to which p points has one user
    auto q(p); // p and q point to the same object
    // object to which p and q point has two users
    
    autor = make_shared<int>(42); // int to which r points has one user
    r = q;  // assign to r, making it point to a different address
    // increase the use count for the object to which q points
    // reduce the use count of the object to which r had pointed
    // the object r had pointed to has no users; that object is automatically freed

**shared_ptrs Automatically Destroy Their Objects and Automatically Free the Associated Memory**

When the last shared_ptr pointing to an object is destroyed, the shared_ptr class automatically destroys the object to which that shared_ptr points. It does so
through another special member function known as a **destructor**. Analogous to its constructors, each class has a destructor. Just as a constructor controls initialization, the destructor controls what happens when objects of that class type are destroyed.

The destructor for shared_ptr decrements the reference count of the object to which that shared_ptr points. If the count goes to zero, the shared_ptr destructor destroys the object to which the shared_ptr points and frees the memory used by that object.

    // factory returns a shared_ptr pointing to a dynamically allocated object
    shared_ptr<Foo> factory(T arg)
    {
    // process arg as appropriate
    // shared_ptr will take care of deleting this memory
    return make_shared<Foo>(arg);
    }
    
    void use_factory(T arg)
    {
    shared_ptr<Foo> p = factory(arg);
    // use p
    } // p goes out of scope; the memory to which p points is automatically freed
    
    shared_ptr<Foo>use_factory(T arg)
    {
    shared_ptr<Foo> p = factory(arg);
    // use p
    return p;  // reference count is incremented when we return p
    } // p goes out of scope; the memory to which p points is not freed

**Programs tend to use dynamic memory for one of three purposes:**

1. They don’t know how many objects they’ll need
2. They don’t know the precise type of the objects they need
3. They want to share data between several objects

**Managing Memory Directly**

The language itself defines two operators that allocate and free dynamic memory. The **new** operator allocates memory, and **delete** frees memory allocated by new.

The new expression constructs an object of type T on the free store and returns a pointer to that object.

    int *pi = new int;  // pi points to a dynamically allocated,
    // unnamed, uninitialized int
    string *ps = new string;  // initialized to empty string
    int *pi = new int;  // pi points to an uninitialized int
    int*pi = new int(1024); // object to which pi points has value 1024
    string *ps = new string(10, '9');  // *ps is "9999999999"
    // vector with ten elements with values from 0 to 9
    vector<int> *pv = new vector<int>{0,1,2,3,4,5,6,7,8,9};
    string*ps1 = new string;  // default initialized to the empty string
    string *ps = new string(); // value initialized to the empty string
    int *pi1 = new int;  // default initialized; *pi1 is undefined
    int *pi2 = new int();  // value initialized to 0; *pi2 is 0

**Memory Exhaustion**

    // if allocation fails, new returns a null pointer
    int *p1 = new int; // if allocation fails, new throws std::bad_alloc
    int *p2 = new (nothrow) int; // if allocation fails, new returns a null pointer

**Freeing Dynamic Memory**

delete p;  // p must point to a dynamically allocated object or be null

Like new, a delete expression performs two actions: It destroys the object to which its given pointer points, and it frees the corresponding memory.The pointer we pass to delete must either point to dynamically allocated memory or be a null pointer. Deleting a pointer to memory that was not allocated by new, or deleting the same pointer value more than once, is undefined.

**Caution:Managing Dynamic Memory Is Error-Prone**
There are three common problems with using new and delete to manage dynamic memory:

1. Forgetting to delete memory. Neglecting to delete dynamic memory is known as a “memory leak,” because the memory is never returned to the free store. Testing for memory leaks is difficult because they usually cannot
be detected until the application is run for a long enough time to actually exhaust memory.
2. Using an object after it has been deleted. This error can sometimes be detected by making the pointer null after the delete.
3. Deleting the same memory twice. This error can happen when two pointers address the same dynamically allocated object. If delete is applied to one of the pointers, then the object’s memory is returned to the free store. If we subsequently delete the second pointer, then the free
store may be corrupted.

You can avoid all of these problems by using smart pointers exclusively.The smart pointer will take care of deleting the memory only when there are no remaining smart pointers pointing to that memory.

**Resetting the Value of a Pointer after a delete Provides Only Limited Protection**

In real systems, finding all the pointers that point to the same memory is surprisingly difficult.

    int *p(new int(42));  // p points to dynamic memory
    auto q = p;  // p and q point to the same memory
    delete p;  // invalidates both p and q
    p = nullptr; // indicates that p is no longer bound to an object

resetting p has no effect on q, which became invalid when we deleted the memory to which p(and q!) pointed.

**Don’t Mix Ordinary Pointers and Smart Pointers**

It is dangerous to use a built-in pointer to access an object owned by a smart pointer, because we may not know when that object is destroyed.

    int *x(new int(1024)); // dangerous: x is a plain pointer, not a smart
    pointer
    process(x);  // error: cannot convert int* to shared_ptr<int>
    process(shared_ptr<int>(x)); // legal, but the memory will be deleted!
    int j = *x;  // undefined: x is a dangling pointer!

**Don’t Use *get* to Initialize or Assign Another Smart Pointer**

Use get only to pass access to the pointer to code that you know will not delete the pointer. In particular, never use get to initialize or assign to another smart pointer.

    shared_ptr<int>p(new int(42)); // reference count is 1
    int *q = p.get();  // ok: but don't use q in any way that might delete its
    pointer
    { // new block
    // undefined: two independent shared_ptrs point to the same memory
    shared_ptr<int>(q);
    } // block ends, q is destroyed, and the memory to which q points is freed
    int foo = *p; // undefined; the memory to which p points was freed

**Smart Pointers and Exceptions**

When we use a smart pointer, the smart pointer class ensures that memory is freed when it is no longer needed even if the block is exited prematurely.

    void f()
    {
    shared_ptr<int> sp(new int(42)); // allocate a new object
    // code that throws an exception that is not caught inside f
    } // shared_ptr freed automatically when the function ends

In contrast, memory that we manage directly is not automatically freed when an exception occurs. If we use built-in pointers to manage memory and an exception
occurs after a new but before the corresponding delete, then that memory won’t be freed.

    void f()
    {
    int *ip = new int(42);  // dynamically allocate a new object
    // code that throws an exception that is not caught inside f
    delete ip;  // free the memory before exiting
    }

If an exception happens between the newand the delete, and is not caught inside f, then this memory can never be freed. There is no pointer to this memory outside
the function f. Thus, there is no way to free this memory.

**Using Our Own Deletion Code**

    void end_connection(connection *p) { disconnect(*p); }
    void f(destination &d /* other parameters*/)
    {
    connection c = connect(&d);
    shared_ptr<connection> p(&c, end_connection);
    // use the connection
    // when f exits, even if by an exception, the connection will be properly closed
    }

**Caution:Smart Pointer Pitfalls**

Smart pointers can provide safety and convenience for handling dynamically allocated memory only when they are used properly. To use smart pointers correctly, we must adhere to a set of conventions:

- Don’t use the same built-in pointer value to initialize (or reset) more than one smart pointer.
- Don’t deletethe pointer returned from get().
- Don’t use get()to initialize or reset another smart pointer.
- If you use a pointer returned by get(), remember that the pointer will become invalid when the last corresponding smart pointer goes away.
- If you use a smart pointer to manage a resource other than memory allocated by new, remember to pass a deleter.

**unique_ptr**

A unique\_ptr “owns” the object to which it points. Unlike shared\_ptr, only one unique\_ptr at a time can point to a given object. The object to which a unique\_ptr points is destroyed when the unique\_ptr is destroyed.

Because a unique\_ptr owns the object to which it points, unique\_ptr does not support ordinary copy or assignment.

Although we can’t copy or assign a unique\_ptr, we can transfer ownership from one (nonconst) unique_ptr to another by calling **release** or **reset**.

The *release* member returns the pointer currently stored in the unique\_ptr and makes that unique\_ptr null.

The *reset* member takes an optional pointer and repositions the unique\_ptr to point to the given pointer.

    unique_ptr<string>p1(new string("Stegosaurus"));
    unique_ptr<string> p2(p1);  // error: no copy for unique_ptr
    unique_ptr<string> p3;
    p3 = p2;  // error: no assign for unique_ptr
    // transfers ownership from p1 (which points to the string Stegosaurus) to p2
    unique_ptr<string> p2(p1.release()); // release makes p1 null
    unique_ptr<string> p3(new string("Trex"));
    // transfers ownership from p3 to p2
    p2.reset(p3.release()); // reset deletes the memory to which p2 had pointed
	p2.release();// WRONG: p2 won't free the memory and we've lost the pointer
	auto p = p2.release(); // ok, but we must remember to delete(p)

**Passing and Returning unique_ptrs**

There is one exception to the rule that we cannot copy a unique\_ptr:We can copy or assign a unique\_ptr that is about to be destroyed. The most common example is
when we return a unique\_ptr from a function.

**weak_ptr**

A weak_ptr is a smart pointer that does not control the lifetime of the object to which it points. Instead, a weak\_ptr points to an object that is managed by a shared\_ptr. Binding a weak\_ptr to a shared\_ptr does not change the reference count of that shared\_ptr. Once the last shared\_ptr pointing to the object goes away, the object itself will be deleted. That object will be deleted even if there are weak\_ptrs pointing to it—hence the name weak\_ptr, which captures the idea that a weak\_ptr shares its object “weakly.”

**The allocator Class**

When we allocate a block of memory, we often plan to construct objects in that memory as needed. In this case, we’d like to decouple memory allocation from object
construction. Decoupling construction from allocation means that we can allocate memory in large chunks and pay the overhead of constructing the objects only when
we actually need to create them. In general, coupling allocation and construction can be wasteful. More importantly, classes that do not have default constructors cannot be dynamically allocated as an array.

The library **allocator** class, which is defined in the **memory** header, lets us separate allocation from construction. It provides type-aware allocation of raw, unconstructed,memory. 

    allocator<string>alloc;  // object that can allocate strings
    auto const p = alloc.allocate(n); // allocate n unconstructed strings

**allocators Allocate Unconstructed Memory**

The memory an allocator allocates is unconstructed.We use this memory by constructing objects in that memory. In the new library the **construct** member takes a pointer and zero or more additional arguments; it constructs an element at the given location. The additional arguments are used to initialize the object being constructed.

We must construct objects in order to use memory returned by allocate. Using unconstructed memory in other ways is undefined.

    auto q = p; // q will point to one past the last constructed element
    alloc.construct(q++);  // *q is the empty string
    alloc.construct(q++, 10, 'c');  // *q is cccccccccc
    alloc.construct(q++, "hi");  // *q is hi!
    cout<< *p << endl; // ok: uses the string output operator
    cout << *q << endl; // disaster: q points to unconstructed memory!
    while(q != p)
    alloc.destroy(--q);// free the  strings  we actually
    allocated

Once the elements have been destroyed, we can either reuse the memory to hold other strings or return the memory to the system. We free the memory by calling deallocate:
***alloc.deallocate(p,n);***

**Algorithms to Copy and Fill Uninitialized Memory**

    // allocate twice as many elements as vi holds
    auto p = alloc.allocate(vi.size() * 2);
    // construct elements starting at p as copies of elements in vi
    auto q = uninitialized_copy(vi.begin(), vi.end(), p);
    // initialize the remaining elements to 42
    uninitialized_fill_n(q, vi.size(), 42);