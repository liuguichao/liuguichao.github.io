---
layout: post
title: "Object-Oriented Programming"
date: 2014-07-18 18:09:52 +0800
comments: true
category: C++
tags: [OOP]
---
The key ideas in object-oriented programming are **data abstraction**, **inheritance**,and **dynamic binding**. Using data abstraction, we can define classes that separate interface from implementation. Through inheritance, we can define classes that model the relationships among similar types. Through dynamic binding, we can use objects of these types while ignoring the details of how they differ.

**Inheritance**

Classes related by inheritance form a hierarchy. Typically there is a **base class** at the root of the hierarchy, from which the other classes inherit, directly or indirectly. These inheriting classes are known as **derived classes**. The base class defines those members that are common to the types in the hierarchy. Each derived class defines those members that are specific to the derived class itself.

In C++, a base class distinguishes functions that are type dependent from those that it expects its derived classes to inherit without change. The base class defines as **virtual** those functions it expects its derived classes to define for themselves.

A derived class must specify the class(es) from which it intends to inherit. It does so in a class derivation list, which is a colon followed by a comma-separated list of base classes each of which may have an optional access specifier.

    class Quote {
    public:
    std::string isbn() const;
    virtual double net_price(std::size_t n) const;
    };
    
    classBulk_quote : public Quote { // Bulk_quote inherits from Quote
    public:
    double net_price(std::size_t) const override;
    };

A derived class must include in its own class body a declaration of all the virtual functions it intends to define for itself. A derived class may include the virtual keyword on these functions but is not required to do so. The new standard lets a derived class explicitly note that it intends a
member function to override a virtual that it inherits. It does so by specifying **override** after its parameter list.

**Dynamic Binding**

In C++, dynamic binding happens when a virtual function is called through a
reference (or a pointer) to a base class.

Through dynamic binding, we can use the same code to process objects of either type Quote or Bulk_quote interchangeably.

    // calculate and print the price for the given number of copies, applying any discounts
    double print_total(ostream &os, const Quote &item, size_t n)
    {
    // depending on the type of the object bound to the item parameter
    // calls either Quote::net_price or Bulk_quote::net_price
    double ret = item.net_price(n);
    os << "ISBN: " << item.isbn() // calls Quote::isbn
    << " # sold: " << n << " total due: " << ret << endl;
    return ret;
    }
    
    // basic has type Quote; bulk has type Bulk_quote
    print_total(cout, basic, 20); //  calls Quote version of net_price
    print_total(cout, bulk, 20);  //  calls Bulk_quote version of net_price

Because the decision as to which version to run depends on the type of
the argument, that decision can’t be made until run time. Therefore, dynamic binding is sometimes known as run-time binding.

**Defining a Base Class**

    class Quote {
    public:
    Quote() = default;  // = default see § 7.1.4 (p. 264)
    Quote(const std::string &book, double sales_price):
    bookNo(book), price(sales_price) { }
    std::string isbn() const { return bookNo; }
    // returns the total sales price for the specified number of items
    // derived classes will override and apply different discount algorithms
    virtual double net_price(std::size_t n) const
    { return n * price; }
    virtual ~Quote() = default; // dynamic binding for the destructor
    private:
    std::string bookNo; // ISBN number of this item
    protected:
    double price = 0.0; // normal, undiscounted price
    };

Base classes ordinarily should define a virtual destructor. Virtual destructors are needed even if they do no work.

**Member Functions and Inheritance**

The base class defines as **virtual** those functions it expects its derived classes to override. When we call a virtual function through a pointer or reference, the call will be dynamically bound. Depending on the type of the object to which the reference orpointer is bound, the version in the base class or in one of its derived classes will be executed.

Any nonstatic member function, other than a constructor, may be virtual. The virtual keyword appears only on the declaration inside the class and may not be used on a function definition that appears outside the class body. A function that is declared as virtual in the base class is implicitly virtual in the derived classes as well. 

**Defining a Derived Class**

    class Bulk_quote : public Quote { // Bulk_quote inherits from Quote
    Bulk_quote() = default;
    Bulk_quote(const std::string&, double, std::size_t,
    double);
    // overrides the base version in order to implement the bulk purchase discount
    policy
    double net_price(std::size_t) const override;
    private:
    std::size_t min_qty = 0; // minimum purchase for the discount to
    apply
    double discount = 0.0;  // fractional discount to apply
    };

**Derived-Class Constructors**

Althougha derived object contains members that it inherits from its base, it cannot directly initialize those members. Like any other code that creates an object of the base-class type, a derived class must use a base-class constructor to initialize its base-class part.

    Bulk_quote(conststd::string& book, double p,
    std::size_t qty, double disc) :
    Quote(book, p), min_qty(qty), discount(disc) { }
    // as before
    };

passes its first two parameters (representing the ISBNand price) to the Quote constructor. That Quote constructor initializes the Bulk_quote’s base-class part (i.e.,the bookNo and pricemembers). When the (empty) Quote constructor body completes, the base-class part of the object being constructed will have been initialized. Next the direct members, min_qty and discount, are initialized. Finally, the (empty) function body of the Bulk_quoteconstructor is run.

**Key Concept: Respecting the Base-Class Interface**

It is essential to understand that each class defines its own interface.
Interactions with an object of a class-type should use the interface of that class, even if that object is the base-class part of a derived object.

As a result, derived-class constructors may not directly initialize the
members of its base class. The constructor body of a derived constructor can assign values to its public or protected base-class members. Although it can assign to those members, it generally should not do so. Like any other user of the base class, a derived class should respect the interface of its base class by using a constructor to initialize its inherited members.

**Inheritance and static Members**

If a base class defines a static member, there is only one such member defined for the entire hierarchy. Regardless of the number of classes derived from a base class, there exists a single instance of each static member.

    class Base {
    public:
    static void statmem();
    };
    class Derived : public Base {
    void f(const Derived&);
    };
    
    void Derived::f(const Derived &derived_obj)
    {
    Base::statmem();  // ok: Base defines statmem
    Derived::statmem(); // ok: Derived inherits statmem
    // ok: derived objects can be used to access static from base
    derived_obj.statmem(); // accessed through a Derived object
    statmem();  // accessed through this object
    }

**Preventing Inheritance**

Sometimes we define a class that we don’t want others to inherit from. Or we might define a class for which we don’t want to think about whether it is appropriate as a base class. Under the new standard, we can prevent a class from being used as a base by following the class name with **final**.

    class NoDerived final { /*  */ }; // NoDerived can't be a base class
    class Base { /*  */ };
    // Last is final; we cannot inherit from Last
    class Last final : Base { /*  */ }; // Last can't be a base class
    class Bad : NoDerived { /*  */ };  // error: NoDerived is final
    class Bad2 : Last { /*  */ };  // error: Last is final

**Static Type and Dynamic Type**

When we use types related by inheritance, we often need to distinguish between the static type of a variable or other expression and the dynamic typeof the object that expression represents. The static type of an expression is always known at compile time—it is the type with which a variable is declared or that an expression yields. The dynamic type is the type of the object in memory that the variable or expression represents. The dynamic type may not be known until run time.

**Key Concept: Polymorphism in C++**
The key idea behind OOP is polymorphism. Polymorphism is derived from a Greek word meaning “many forms.” We speak of types related by inheritance as polymorphic types, because we can use the “many forms” of these types while ignoring the differences among them. The fact that the static and dynamic types of references and pointers can differ is the cornerstone of how C++ supports polymorphism.

When we call a function defined in a base class through a reference or pointer to the base class, we do not know the type of the object on which that member is executed. The object can be a base-class object or an object of a derived class. If the function is virtual, then the decision as to which function to run is delayed until run time. The version of the virtual function that is run is the one defined by the type of the object to which the reference is bound or to which the pointer points.

On the other hand, calls to nonvirtual functions are bound at compile time.
Similarly, calls to any function (virtual or not) on an object are also bound at compile time. The type of an object is fixed and unvarying—there is nothing we can do to make the dynamic type of an object differ from its static type. Therefore, calls made on an object are bound at compile time to the version defined by the type of the object.

Virtuals are resolved at run time only if the call is made through a
**reference** or **pointer**. Only in these cases is it possible for an object’s dynamic type to differ from its static type.

**Virtual Functions in a Derived Class**

A function that is virtual in a base class is implicitly virtual in its
derived classes. When a derived class overrides a virtual, the parameters in the base and derived classes must match exactly.

**The final and override Specifiers**

The compiler will reject a program if a function marked **override** does not override an existing virtual function:

    struct B {
    virtual void f1(int) const;
    virtual void f2();
    void f3();
    };
    struct D1 : B {
    void f1(int) const override; // ok: f1 matches f1 in the base
    void f2(int) override; // error: B has no f2(int) function
    void f3() override;  // error: f3 not virtual
    void f4() override;  // error: B doesn't have a function named f4
    };

We can also designate a function as **final**. Any attempt to override a function that has been defined as final will be flagged as an error:

    struct D2 : B {
    // inherits f2() and f3() from B and overrides f1(int)
    void f1(int) const final; // subsequent classes can't override f1
    (int)
    };
    struct D3 : D2 {
    void f2();  // ok: overrides f2 inherited from the indirect base,
    B
    void f1(int) const; // error: D2 declared f2 as final
    };

**Virtual Functions and Default Arguments**

Like any other function, a virtual function can have default arguments. If a call uses a default argument, the value that is used is the one defined by the static type through which the function is called.

That is, when a call is made through a reference or pointer to base, the default argument(s) will be those defined in the base class. The base-class arguments will be used even when the derived version of the function is run. In this case, the derived function will be passed the default arguments defined for the base-class version of the function. If the derived function relies on being passed different arguments, the
program will not execute as expected.

**Circumventing the Virtual Mechanism**

In some cases, we want to prevent dynamic binding of a call to a virtual function; we want to force the call to use a particular version of that virtual. We can use the scope operator to do so. 

    //  calls the version from the base class regardless of the dynamic type of baseP
    double undiscounted = baseP->Quote::net_price(42);

Ordinarily,only code inside member functions (or friends) should need to use the scope operator to circumvent the virtual mechanism.

Why might we wish to circumvent the virtual mechanism? The most common reason is when a derived-class virtual function calls the version from the base class. In such cases, the base-class version might do work common to all types in the hierarchy. The versions defined in the derived classes would do whatever additional work is particular to their own type.

**Pure Virtual Functions**

Unlike ordinary virtuals, a pure virtual function does not have to be defined. We specify that a virtual function is a pure virtual by writing **= 0** in place of a function body (i.e., just before the semicolon that ends the declaration). The = 0 may appear only on the declaration
of a virtual function in the class body.

    // class to hold the discount rate and quantity
    // derived classes will implement pricing strategies using these data
    class Disc_quote : public Quote {
    public:
    Disc_quote() = default;
    Disc_quote(const std::string& book, double price,
    std::size_t qty, double disc):
    Quote(book, price),
    quantity(qty), discount(disc) { }
    double net_price(std::size_t) const = 0;
    protected:
    std::size_t quantity = 0; //  purchase size for the discount to apply
    double discount = 0.0;  //  fractional discount to apply
    };

**Classes with Pure Virtuals Are Abstract Base Classes**

A class containing (or inheriting without overridding) a pure virtual function is an abstract base class. An abstract base class defines an interface for subsequent classes to override. We cannot (directly) create objects of a type that is an abstract base class. 

**A Derived Class Constructor Initializes Its Direct Base Class Only**

**protected Members**

As we’ve seen, a class uses protected for those members that it is willing to share with its derived classes but wants to protect from general access. The protected specifier can be thought of as a blend of private and public:

- Like private, protected members are inaccessible to users of the class.
- Like public, protected members are accessible to members and friends of
classes derived from this class.

In addition, protected has another important property:

- A derived class member or friend may access the protected members of the
base class only through a derived object. The derived class has no special
access to the protected members of base-class objects.

**public, private, and protected Inheritance**

The derivation access specifier has no effect on whether members (and friends) of a derived class may access the members of its own direct base class. Access to the members of a base class is controlled by the access specifiers in the base class itself.

The purpose of the derivation access specifier is to control the access that users of the derived class—including other classes derived from the derived class.

The derivation access specifier used by a derived class also controls access from classes that inherit from that derived class

    class Base {
    public:
    void pub_mem();  // public member
    protected:
    int prot_mem;  // protected member
    private:
    char priv_mem;  // private member
    };
    struct Pub_Derv : public Base {
    // ok: derived classes can access protected members
    int f() { return prot_mem; }
    // error: private members are inaccessible to derived classes
    char g() { return priv_mem; }
    };
    struct Priv_Derv : private Base {
    // private derivation doesn't affect access in the derived class
    int f1() const { return prot_mem; }
    };
    
    Pub_Derv d1;  //  members inherited from Base are public
    Priv_Derv d2;  //  members inherited from Base are private
    d1.pub_mem();  //  ok: pub_mem is public in the derived class
    d2.pub_mem();  //  error: pub_mem is private in the derived clas
    
    struct Derived_from_Public : public Pub_Derv {
    // ok: Base::prot_mem remains protected in Pub_Derv
    int use_base() { return prot_mem; }
    };
    struct Derived_from_Private : public Priv_Derv {
    // error: Base::prot_mem is private in Priv_Derv
    int use_base() { return prot_mem; }
    };

**Key Concept: Class Design and protected Members**

In the absence of inheritance, we can think of a class as having two different kinds of users: ordinary users and implementors. Ordinary users write code that uses objects of the class type; such code can access only the public (interface) members of the class. Implementors write the code contained in the members and friends of the class. The members and friends of the class can access both the public and private(implementation) sections.

Under inheritance, there is a third kind of user, namely, derived classes. A base class makes protected those parts of its implementation that it is
willing to let its derived classes use. The protected members remain
inaccessible to ordinary user code; private members remain inaccessible to
derived classes and their friends.

Like any other class, a class that is used as a base class makes its interface members public. A class that is used as a base class may divide its implementation into those members that are accessible to derived classes and those that remain accessible only to the base class and its friends. An implementation member should be protected if it provides an operation or data that a derived class will need to use in its own implementation. Otherwise, implementation members should be private.

**Friendship and Inheritance**

Just as friendship is not transitive, friendship is also not inherited.
Friends of the base have no special access to members of its derived classes, and friends of a derived class have no special access to the base class.

**Exempting Individual Members**

Sometimes we need to change the access level of a name that a derived class
inherits. We can do so by providing a **using** declaration.

    class Base {
    public:
    std::size_t size() const { return n; }
    protected:
    std::size_t n;
    };
    class Derived : private Base {  //  note: private inheritance
    public:
    // maintain access levels for members related to the size of the object
    using Base::size;
    protected:
    using Base::n;
    };

**Default Inheritance Protection Levels**

By default, a derived class defined with the class keyword has private inheritance; a derived class defined with struct has public inheritance.

It is a common misconception to think that there are deeper differences between classes defined using the struct keyword and those defined using class. The only differences are the default access specifier for members and the default derivation access specifier. There are no other distinctions.

**Name Collisions and Inheritance**

A derived-class member with the same name as a member of the base class
hides direct use of the base-class member. Using the Scope Operator to Use Hidden Members.

**As Usual, Name Lookup Happens before Type Checking**

As in any other scope, if a member in a derived class (i.e., in an inner scope) has the same name as a base class member (i.e., a name defined in an outer scope), then the derived member hides the base-class member within the scope of the derived class. The base member is hidden even if the functions have different parameter lists.

    struct Base {
    int memfcn();
    };
    structDerived : Base {
    int memfcn(int);  // hides memfcn in the base
    };
    Derived d; Base b;
    b.memfcn();  //  calls Base::memfcn
    d.memfcn(10);  //  calls Derived::memfcn
    d.memfcn();  //  error: memfcn with no arguments is hidden
    d.Base::memfcn(); //  ok: calls Base::memfcn

**Virtual Functions and Scope**

We can now understand why virtual functions must have the same parameter list in the base and derived classes. If the base and derived members took
arguments that differed from one another, there would be no way to call the derived version through a reference or pointer to the base class.

**Overriding Overloaded Functions**

As with any other function, a member function (virtual or otherwise) can be
overloaded. A derived class can override zero or more instances of the overloaded functions it inherits. If a derived class wants to make all the overloaded versions available through its type, then it must override all of them or none of them.

Instead of overriding every base-class version that it inherits, a derived class can provide a **using** declaration for the overloaded member. A using declaration specifies only a name; it may not specify a parameter list. 

**Virtual Destructors**

The primary direct impact that inheritance has on copy control for a base class is that a base class generally should define a virtual destructor. The destructor needs to be virtual to allow objects in the inheritance hierarchy to be dynamically allocated. If a class defines a destructor—even if it uses **= default** to use the synthesized version—the compiler will not synthesize a move operation for that class. 

**Inherited Constructors**

Under the new standard, a derived class can reuse the constructors defined by its direct base class. Ad erived class inherits its base-class constructors by providing a **using** declaration that names its (direct) base class. 