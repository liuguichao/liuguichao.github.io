---
layout: post
title: "Overloaded Operations and Conversions"
date: 2014-07-18 13:44:09 +0800
comments: true
category: C++
tags: [Overloaded]
---
When an overloaded operator is a member function, **this** is bound to the
**left-hand operand**. Member operator functions have one less (explicit)
parameter than the number of operands.

**Calling an Overloaded Operator Function Directly**

``` C++
// equivalent calls to a nonmember operator function
data1 + data2;  // normal expression
operator+(data1, data2); // equivalent function cal

data1+= data2;  // expression-based ''call''
data1.operator+=(data2);  // equivalent call to a member operator function
```

Ordinarily,the comma, address-of, logical AND, and logical OR operators
should notbe overloaded.Users of the class will be surprised if these operators behave differently from their normal meanings.

**Choosing Member or Nonmember Implementation**

- The assignment (=), subscript ([]), call (()), and member access arrow (->) operators must be defined as members.
- The compound-assignment operators ordinarily oughtto be members. However,
unlike assignment, they are not required to be members.
- Operators that change the state of their object or that are closely tied to their given type—such as increment, decrement, and dereference—usually should be members.
- Symmetric operators—those that might convert either operand, such as the
arithmetic, equality, relational, and bitwise operators—usually should be defined as ordinary nonmember functions.

When we define an operator as a member function, then the left-hand operand
must be an object of the class of which that operator is a member. 

``` C++
strings = "world";
string t = s + "!";  // ok: we can add a const char* to a string
string u = "hi" + s; // would be an error if + were a member of string
```

If operator+ were a member of the string class, the first addition would be
equivalent to s.operator+("!"). Likewise, "hi" + swould be equivalent to
"hi".operator+(s). However, the type of "hi" is const char*, and that is a
built-in type; it does not even have member functions.

**Overloading the Output Operator <<**

Ordinarily, the first parameter of an output operator is a reference to a nonconst ostream object. The ostream is nonconst because writing to the stream changes its state. The parameter is a reference because we cannot copy an ostream object.

The second parameter ordinarily should be a reference to const of the class type we want to print. The parameter is a reference to avoid copying the argument. It can be const because (ordinarily) printing an object does not change that object.

To be consistent with other output operators, operator<< normally returns its ostream parameter.

``` C++
ostream &operator<<(ostream &os, const Sales_data &item)
{
    os << item.isbn() << " " << item.units_sold << " "
    << item.revenue << " " << item.avg_price();
    return os;
}
```

Generally,output operators should print the contents of the object, with minimal formatting. They should not print a newline.

**IO Operators Must Be Nonmember Functions**

Input and output operators that conform to the conventions of the iostream library must be ordinary nonmember functions. These operators cannot be members of our own class. If they were, then the left-hand operand would have to be an object of our class type.

Thus,if we want to define the IO operators for our types, we must define them as nonmember functions. Of course, IO operators usually need to read or write the **nonpublic** data members. As a consequence, IO operators usually must be declared as **friends**.

**Overloading the Input Operator >>**

Ordinarily the first parameter of an input operator is a reference to the stream from which it is to read, and the second parameter is a reference to the (nonconst) object into which to read. The operator usually returns a reference to its given stream. The second parameter must be nonconst because the purpose of an input operator is to read data into this object.

``` C++
istream &operator>>(istream &is, Sales_data &item)
{
    double price;  // no need to initialize; we'll read into price before we use it
    is >> item.bookNo >> item.units_sold >> price;
    if (is)  // check that the inputs succeeded
    item.revenue = item.units_sold * price;
    else
    item = Sales_data(); // input failed: give the object the default state
    return is;
}
```

Input operators must deal with the possibility that the input might fail; output operators generally don’t bother.

**Indicating Errors**

Usually an input operator should set only the failbit. Setting eof bit
would imply that the file was exhausted, and setting badbit would indicate that the stream was corrupted. These errors are best left to the IO library itself to indicate.

**Arithmetic and Relational Operators**

Ordinarily,we define the arithmetic and relational operators as nonmember functions in order to allow conversions for either the left- or right-hand operand. These operators shouldn’t need to change the state of either operand, so the parameters are ordinarily references to **const**.

``` C++
// assumes that both objects refer to the same book
Sales_data operator+(const Sales_data &lhs, const Sales_data &rhs)
{
    Sales_data sum = lhs;  // copy data members from lhs into sum
    sum += rhs;  // add rhs into sum
    return sum;
}
```

Classes that define both an arithmetic operator and the related compound assignment ordinarily ought to implement the arithmetic operator by using
the compound assignment.

**Equality Operators**
 
 ``` C++   
bool operator==(const Sales_data &lhs, const Sales_data &rhs)
{
    return lhs.isbn() == rhs.isbn() &&
    lhs.units_sold == rhs.units_sold &&
    lhs.revenue == rhs.revenue;
}
bool operator!=(const Sales_data &lhs, const Sales_data &rhs)
{
    return !(lhs == rhs);
}
```

Classes for which there is a logical meaning for equality normally should define operator==. Classes that define ==make it easier for users to use
the class with the library algorithms.

**Relational Operators**

Classes for which the equality operator is defined also often (but not always) have relational operators. In particular, because the associative containers and some of the algorithms use the less-than operator, it can be useful to define an **operator<**.

Ordinarily the relational operators should

1. Define an ordering relation that is consistent with the requirements for use as a key to an associative container; and
2. Define a relation that is consistent with ==if the class has both operators. In particular, if two objects are !=, then one object should be <the other.

If a single logical definition for <exists, classes usually should define the <operator. However, if the class also has ==, define <only if the definitions of <and ==yield consistent results.

**Assignment Operators**

In addition to the copy- and move-assignment operators that assign one object of the class type to another object of the same type,a class can define additional assignment operators that allow other types as the
right-hand operand.

As one example, in addition to the copy- and move-assignment operators, the library **vector** class defines a third assignment operator that takes a braced list of elements.

``` C++
vector<string> v;
v = {"a", "an", "the"};

classStrVec {
public:
    StrVec &operator=(std::initializer_list<std::string>);
    // other members as in § 13.5 (p. 526)
};
```

Assignment operators can be overloaded. Assignment operators, regardless of parameter type, must be defined as member functions.

**Compound-Assignment Operators**

Compound assignment operators are not required to be members. However, we prefer to define all assignments, including compound assignments, in the class. For consistency with the built-in compound assignment, these operators should return a reference to their left-hand operand. 

Assignment operators must, and ordinarily compound-assignment operators
should, be defined as members. These operators should return a reference to
the left-hand operand.

**Subscript Operator**

Classes that represent containers from which elements can be retrieved by position often define the subscript operator, **operator[]**. 

The subscript operator must be a member function.

To be compatible with the ordinary meaning of subscript, the subscript operator usually returns a **reference** to the element that is fetched. By returning a reference, subscript can be used on either side of an assignment. Consequently, it is also usually a good idea to define both const and nonconst versions of this operator. When applied to a const object, subscript should return a reference to const so that it is
not possible to assign to the returned object.

``` C++
class StrVec {
public:
    std::string& operator[](std::size_t n)
    { return elements[n]; }
    const std::string& operator[](std::size_t n) const
    { return elements[n]; }
    // other members as in § 13.5 (p. 526)
private:
    std::string *elements;  // pointer to the first element in the array
};
```
**Increment and Decrement Operators**

The increment (++) and decrement (--) operators are most often implemented for iterator classes. These operators let the class move between the elements of a sequence.

Classes that define increment or decrement operators should define both the prefix and postfix versions. These operators usually should be defined as members.

**Defining Prefix Increment/Decrement Operators**

To be consistent with the built-in operators, the prefix operators should return a reference to the incremented or decremented object.

``` C++
// prefix: return a reference to the incremented/decremented object
StrBlobPtr& StrBlobPtr::operator++()
{
    // if curr already points past the end of the container, can't increment it
    check(curr, "increment past end of StrBlobPtr");
    ++curr;  // advance the current state
    return *this;
}
StrBlobPtr& StrBlobPtr::operator--()
{
    // if curr is zero, decrementing it will yield an invalid subscript
    --curr;  // move the current state back one element
    check(-1, "decrement past begin of StrBlobPtr");
    return *this;
}
```

**Differentiating Prefix and Postfix Operators**

The postfix versions take an extra (unused) parameter of type int. Its sole purpose is to distinguish a postfix function from the prefix version.

To be consistent with the built-in operators, the postfix operators should return the old (unincremented or undecremented) value. That value is
returned as a value, not a reference.

``` C++
class StrBlobPtr {
public:
    // increment and decrement
    StrBlobPtr operator++(int);  // postfix operators
    StrBlobPtr operator--(int);
    // other members as before
};
```

**Calling the Postfix Operators Explicitly**

If we want to call the postfix version using a function call, then we must pass a value for the integer argument. The value passed usually is ignored but is necessary in order to tell the compiler to use the postfix version.

``` C++
StrBlobPtrp(a1); // p points to the vector inside a1
p.operator++(0);  // call postfix operator++
p.operator++();  // call prefix  operator++
```

**Member Access Operators**

The dereference (*) and arrow (->) operators are often used in classes that
represent iterators and in smart pointer classes.Operator arrow must be a member. The dereference operator is not required to be a member but usually should be a member as well.
``` C++
class StrBlobPtr {
public:
    std::string& operator*() const
    { auto p = check(curr, "dereference past end");
    return (*p)[curr];  // (*p) is the vector to which this object points
    }
    std::string* operator->() const
    { // delegate the real work to the dereference operator
    return & this->operator*();
    }
    // other members as before
};
```

**Constraintson the Return from Operator Arrow**

The overloaded arrow operator must return either a pointer to a class type or an object of a class type that defines its own operator arrow.

**Function-Call Operator**

Classes that overload the call operator allow objects of its type to be used as if they were a function. Because such classes can also store state, they can be more flexible than ordinary functions.

``` C++
struct absInt {
int operator()(int val) const {
return val < 0 ? -val : val;
}
};
inti = -42;
absInt absObj;  // object that has a function-call operator
int ui = absObj(i); // passes i to absObj.operator()
```

The function-call operator must be a member function. A class may define
multiple versions of the call operator, each of which must differ as to the
number or types of their parameters.

Objects of classes that define the call operator are referred to as **function objects**.Such objects “act like functions” because we can call them.

**Function-Object Classes with State**

Function objects are most often used as arguments to the generic algorithms. For example, we can use the library for_each algorithm.

``` C++
class PrintString {
public:
PrintString(ostream &o = cout, char c = ' '):
os(o), sep(c) { }
void operator()(const string &s) const { os << s << sep;
}
private:
ostream &os;  // stream on which to write
char sep;  // character to print after each output
};

PrintString printer;  // uses the defaults; prints to cout
printer(s);  // prints s followed by a space on cout
PrintString errors(cerr, '\n');
errors(s);  // prints s followed by a newline on cerr

for_each(vs.begin(),vs.end(), PrintString(cerr, '\n'));
```

**Lambdas Are Function Objects**

When we write a lambda, the compiler translates that expression into an unnamed object of an unnamed class.The classes generated from a lambda contain an overloaded function-call operator.

``` C++
// sort words by size, but maintain alphabetical order for words of the same size
stable_sort(words.begin(), words.end(),
[](const string &a, const string &b)
{ return a.size() < b.size();});

class ShorterString {
public:
bool operator()(const string &s1, const string &s2) const
{ return s1.size() < s2.size(); }
};

stable_sort(words.begin(),words.end(), ShorterString());
```

**Classes Representing Lambdas with Captures**

``` C++
// get an iterator to the first element whose size() is >= sz
auto wc = find_if(words.begin(), words.end(),[sz](const string &a)

class SizeComp {
SizeComp(size_t n): sz(n) { } // parameter for each captured variable
// call operator with the same return type, parameters, and body as the lambda
bool operator()(const string &s) const
{ return s.size() >= sz; }
private:
size_t sz; // a data member for each variable captured by value
};

// get an iterator to the first element whose size() is >= sz
auto wc = find_if(words.begin(), words.end(), SizeComp(sz));
```

**Library-Defined Function Objects**

The standard library defines a set of classes that represent the arithmetic, relational,and logical operators. Each class defines a call operator that applies the named operation. These classes are templates to which we supply a single type. That type specifies the parameter type for the call operator.

![](http://i.imgur.com/8UZnARL.jpg)

``` C++
plus<int> intAdd;  // function object that can add two int values
negate<int> intNegate;  // function object that can negate an int value
// uses intAdd::operator(int, int) to add 10 and 20
int sum = intAdd(10, 20);  // equivalent to sum = 30
sum = intNegate(intAdd(10, 20));  // equivalent to sum = 30
// uses intNegate::operator(int) to generate -10 as the second parameter
// to intAdd::operator(int, int)
sum = intAdd(10, intNegate(10));  // sum = 0
```

**Using a Library Function Object with the Algorithms**

The function-object classes that represent operators are often used to override the default operator used by an algorithm. 

``` C++
// passes a temporary function object that applies the < operator to two strings
sort(svec.begin(), svec.end(), greater<string>());
sort(svec.begin(), svec.end(), less<string>());
```

**Callable Objects and function**

C++ has several kinds of callable objects: functions and pointers to functions,lambdas, objects created by bind, and classes that overload the function-call operator.

two callable objects with different types may share the same **call
signature**. The call signature specifies the type returned by a call to the object and the argument type(s) that must be passed in the call. A call signature corresponds to a function type. For example: int(int,int).

**The Library function Type**

**function** is a template. As with other templates we’ve used, we must specify additional information when we create a function type. In this case, that information is the **call signature** of the objects that this particular function type can represent.

``` C++
// table of callable objects corresponding to each binary operator
// all the callables must take two ints and return an int
// an element can be a function pointer, function object, or lambda
map<string,function<int(int, int)>> binops = {
{"+", add},  // function pointer
{"-", std::minus<int>()},  // library function object
{"/",  div()},  // user-defined function object
{"*", [](int i, int j) { return i * j; }}, // unnamed
lambda
{"%", mod} };  // named lambda object

binops["+"](10,5); // calls add(10, 5)
binops["-"](10, 5); // uses the call operator of the minus<int> object
binops["/"](10, 5); // uses the call operator of the div object
binops["*"](10, 5); // calls the lambda function object
binops["%"](10, 5); // calls the lambda function object
```

The function class in the new library is not related to classes named
unary_function and binary_function that were part of earlier versions
of the library. These classes have been deprecated by the more general bind
function.

**Conversion Operators**

A conversion operatoris a special kind of member function that converts a value of a class type to a value of some other type. A conversion function typically has the general form **operator type() const;**

where type represents a type. Conversion operators can be defined for any type (other than void) that can be a function return type. Conversions to
an array or a function type are not permitted. Conversions to pointer types—both data and function pointers—and to reference types are allowed.

Conversion operators have no explicitly stated return type and no parameters, and they must be defined as member functions. Conversion operations ordinarily should not change the object they are converting. As a result, conversion operators usually should be defined as const members.

**Defining a Class with a Conversion Operator**

``` C++
class SmallInt {
public:
SmallInt(int i = 0): val(i)
{
if (i < 0 || i > 255)
throw std::out_of_range("Bad SmallInt value");
}
operator int() const { return val; }
private:
std::size_t val;
};

SmallInt si;
si = 4; // implicitly converts 4 to SmallInt then calls SmallInt::operator=
si + 3; // implicitly converts si to int followed by integer addition
```

**Conversion Operators Can Yield Suprising Results**

In practice, classes rarely provide conversion operators. Too often users are more likely to be surprised if a conversion happens automatically than to be helped by the existence of the conversion. However, there is one important exception to this rule of thumb: It is not uncommon for classes to define conversions to bool.

``` C++
int i = 42;
cin << i; // this code would be legal if the conversion to bool were not explicit!
```
This program attempts to use the output operator on an input stream. There is no << defined for istream, so the code is almost surely in error. However, this code could use the bool conversion operator to convert cin to bool. The resulting bool value would then be promoted to int and used as the left-hand operand to the built-in version of the left-shift operator. The promoted boolvalue (either 1 or 0) would be shifted left 42 positions.

**explicit Conversion Operators**

To prevent such problems, the new standard introduced explicit conversion operators.

``` C++
class SmallInt {
public:
// the compiler won't automatically apply this conversion
explicit operator int() const { return val; }
// other members as before
};

SmallInt si = 3;  // ok: the SmallInt constructor is not explicit
si + 3; // error: implicit is conversion required, but operator int is explicit
static_cast<int>(si) + 3; // ok: explicitly request the conversion
```

If the conversion operator is explicit, we can still do the conversion. However, with one exception, we must do so explicitly through a cast.
The exception is that the compiler will apply an explicit conversion to an
expression used as a **condition**. 

**Conversion to bool**

In earlier versions of the library, the IO types defined a conversion to void*. They did so to avoid the kinds of problems illustrated above. Under the new standard, the IO library instead defines an explicit conversion to bool.
``` C++
while(std::cin >> value)
```
The condition in the while executes the input operator, which reads into value and returns cin. To evaluate the condition, cin is implicitly converted by the istream operator bool conversion function. That function returns true if the conditionstate of cin is good, and false other wise.

Conversion to bool is usually intended for use in conditions. As a result,
operator bool ordinarily should be defined as explicit.