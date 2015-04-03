---
layout: post
title: "classes concept"
date: 2014-07-10 15:43:45 +0800
comments: true
category: C++
tags: [class]
---
**Note**

1. Functions defined in the class are implicitly *inline*
2. Ordinarily,nonmember functions that are part of the interface of a class should be declared in the same header as the class itself.
3. The compiler generates a default constructor automatically only if a class declares noconstructors.
4. Classes that have members of built-in or compound type usually should rely on the synthesized default constructor only if all such members have in-class initializers.
5. The only difference between using class and using struct to define a class is the default access level.As a matter of programming style, when we define a class intending for all of its members to be public, we use struct. If we intend to have private members,then we use class.
6. inline member functions should be defined in the same header as the corresponding class definition.
7. When we provide an in-class initializer, we must do so following an = sign or inside braces.
8. Functions that return a reference are lvalues, which means that they return the object itself, not a copy of the
object.A const member function that returns *this as a reference should have a return type that is a reference to const.
9. Each class controls which classes or functions are its friends.
10. Member function definitions are processed after the compiler processes all of the declarations in the class.
11. Definitions of type names usually should appear at the beginning of a class.That way any member that uses that type will be seen after the type name has already been defined.
12. We must use the constructor initializer list to provide values for members that are const, reference, or of a class type that does not have a default constructor.
13. It is a good idea to write constructor initializers in the same order as the members are declared. Moreover, when possible, avoid using members to initialize other members.
14. As with any class member, when we refer to a class static member outside the class body, we must specify the class in which the member is defined.The *static* keyword, however, is used only on the declaration inside the class body.


**Friends**

A class can allow another class or function to access its nonpublic members by making that class or function a *friend*. A class makes a function its friend by including
a declaration for that function preceded by the keyword *friend*.

Friend declarations may appear only inside a class definition; they may appear anywhere in the class. Friends are not members of the class and are not affected by
the access control of the section in which they are declared. 

To make a friend visible to users of the class, we usually declare each friend (outside the class) in the same header as the class itself. 

**Benefits of Encapsulation**

- User code cannot inadvertently corrupt the state of an encapsulated object.
- The implementation of an encapsulated class can change over time without requiring changes in user-level code.

**mutable Data Members**

It sometimes (but not very often) happens that a class has a data member that we want to be able to modify, even inside a const member function. We indicate such
members by including the *mutable* keyword in their declaration.A mutable data member is never const, even when it is a member of a const object. Accordingly, a const member function may change a mutable member.

**Class Declarations**

Because a class is not defined until its class body is complete, a class cannot have data members of its own type. However, a class is considered declared (but not yet defined) as soon as its class name has been seen. Therefore, a class can have data members that are pointers or references to its own type.

**Friendship Revisited**

	//Friendship between Classes
    class Screen {
    // Window_mgr memberscan access the private parts of class Screen
    	friend class Window_mgr;
    // ... rest of the Screen class
    };
	//Making A Member Function a Friend
	classScreen {
	// Window_mgr::clear must have been declared before class Screen
		friend void Window_mgr::clear(ScreenIndex);
	// ... rest of the Screen class
	};

Making a member function a friend requires careful structuring of our programs to accommodate interdependencies among the declarations and definitions. In this example, we must order our program as follows:
•First, define the Window_mgr class, which declares, but cannot define, clear.Screen must be declared before clear can use the members of Screen.
•Next, define class Screen, including a friend declaration for clear.
•Finally, define clear, which can now refer to the members in Screen.

**Order of Member Initialization**

Members are initialized in the order in which they appear in the class definition: The first member is initialized first, then the next, and so on. The order in which initializers appear in the constructor initializer list does not change the order of initialization.
The order of initialization often doesn’t matter. However, if one member is initialized in terms of another, then the order in which members are initialized is crucially
important.

**Default Arguments and Constructors**

    // defines the default constructor as well as one that takes a string argument
	Sales_data(std::string s = ""): bookNo(s) { }
we can call this constructor with no arguments, this
constructor defines a default constructor for our class.
A constructor that supplies default arguments for all its parameters also defines the default constructor.

**Delegating Constructors**

    class Sales_data {
    public:
    // nondelegating constructor initializes members from corresponding arguments
    Sales_data(std::string s, unsigned cnt, double price):
    bookNo(s), units_sold(cnt), revenue(cnt*price) {
    }
    // remaining constructors all delegate to another constructor
    Sales_data(): Sales_data("", 0, 0) {}
    Sales_data(std::string s): Sales_data(s, 0,0) {}
    Sales_data(std::istream &is): Sales_data()
    { read(is, *this); }
    // other members as before
    };

**Implicit Class-Type Conversions**

A constructor that can be called with a single argument defines an implicit conversion from the constructor’s parameter type to the class type.

    // ok: explicit conversion to string, implicit conversion to Sales_data
    item.combine(string("9-999-99999-9"));
    // ok: implicit conversion to string, explicit conversion to Sales_data
    item.combine(Sales_data("9-999-99999-9"));

We can prevent the use of a constructor in a context that requires an implicit conversion by declaring the constructor as *explicit*:
explicit Sales_data(std::istream&);The *explicit* keyword is used only on the constructor declaration inside
the class. It is not repeated on a definition made outside the class body.explicit Constructors Can Be Used Only for Direct Initialization.When a constructor is declared *explicit*, it can be used only with the
direct form of initialization. Moroever, the compiler will not use this constructor in an automatic conversion.

**Aggregate Classes**

An aggregate class gives users direct access to its members and has special initialization syntax. A class is an aggregate if •All of its data members are public
•It does not define any constructors
•It has no in-class initializers
•It has no base classes or virtual functions

    struct Data {int ival;string s;};
    // val1.ival = 0; val1.s = string("Anna")
    Data val1 = { 0, "Anna" };
**static Class Members**

Classes sometimes need members that are associated with the class, rather than with individual objects of the class type. For example, a bank account class might need a data member to represent the current prime interest rate. In this case, we’d want to associate the rate with the class, not with each individual object. From an efficiency
standpoint, there’d be no reason for each object to store the rate. Much more importantly, if the rate changes, we’d want each object to use the new value.

**Declaring static Members**

We say a member is associated with the class by adding the keyword *static* to its declaration. Like any other member, static members can be public or private.The type of a static data member can be const, reference, array, class type, and so forth.

    classAccount {
    public:
    void calculate() { amount += amount * interestRate; }
    static double rate() { return interestRate; }
    static void rate(double);
    private:
    std::string owner;
    double amount;
    static double interestRate;
    static double initRate();
    };

The static members of a class exist outside any object. Objects do not contain data associated with static data members. Thus, each Account object will contain two
data members—owner and amount. There is only one interestRate object that will be shared by all the Account objects.Similarly, static member functions are not bound to any object; they do not have a this pointer. As a result, static member functions may not be declared as const, and we may not refer to this in the body of a static member. This restriction applies both to explicit uses of this and to implicit uses of this by calling a nonstatic member.We can access a static member directly through the scope operator:Account::rate().Member functions can use static members directly, without the scope operator.
When we define a static member outside the class, we
do not repeat the static keyword. The keyword appears only with the declaration inside the class body.

Because static data members are not part of individual objects of the class type,they are not defined when we create objects of the class. As a result, they are not
initialized by the class’ constructors. Moreover, in general, we may not initialize a static member inside the class. Instead, we must define and initialize each static
data member outside the class body. Like any other object, a static data member may be defined only once.Like global objects, static data members are defined outside any
function. Hence, once they are defined, they continue to exist until the program completes.

**C++ 11**

    typedef std::string::size_type pos;
    // alternative way to declare 
    using pos = std::string::size_type;

	// needed because Screen has another constructor
	Screen() = default; 

  	//specify default value with an in-class initializer
	std::vector<Screen> screens{Screen(24, 80, ' ') };