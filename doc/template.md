---
title: Volt Templates
layout: page
---

Volt does not wish to add a second template language ontop of itself, as in C++ and others. These systems are often useful, but the power comes at a cost to usability. However, we believe a compromise can be struct -- absolutely no way of defining functions and types that can be applied to multiple types can lead to code duplication and bugs.

So what follows is a modest proposal to add simple mixin templates to Volt. A mixin template can be defined for `struct`s, `union`s, `interface`s, `fn`s, and `class`es.

    struct LinkedList!(T)
    {
        data: T;
        next: LinkedDoubleList*;
    }

    struct I32List = LinkedList!i32

The simple example above demonstrates several things. A mixin template defines an identifier that is associated with a series of tokens. Multiple identifiers can be defined:

    struct LinkedDoubleList!(T, Y)
    {
        data1: T;
        data2: Y;
        next: LinkedDoubleList*;
    }
    
    struct I3216PtrList = LinkedDoubleList!(i32, i16*);
 
 Those identifiers are valid only for the scope of the template -- that is, until the brace that closes the `struct`/`class` etc. The next thing to note is that the name of the mixin template is treated specially. It would not be valid to use `LinkedList` on its own anywhere else, but inside the template it means 'the type that is being instantiated'. That is to say, in the first example it is as if `next: LinkedList!i32*;` was written. However, note that could not be used, as the instantiation syntax is only valid in one place.
 
As mentioned above, the mixin templates cannot be used directly. A function cannot take a `LinkedList!(i32)`. To make a concrete type (that is to say, a type that can be initialised) you must use an initialisation expression.

Like how we use the keyword `ref` when passing an argument to a parameter marked with `ref`, to make it obvious to the casual reader what is going on, every template initialisation expression starts with the kind of type it is defining. That is to say, `struct`, `union`, `interface`, `fn`, or `class`.

     struct I32List = LinkedList!i32
 
 A few notes about the above syntax. It goes without saying, that the type must match. `class I32List = ...` would of course, generate an error. The right hand side has the name of the template being instantiated, and then an exclamation mark to denote the beginning of the template parameters. The above is a short hand. The complete syntax is wrapped in parens.
 
     struct I32List = LinkedList!(i32)

The parameters must be an identifier, or the sigils that follow a standard type declaration (`*`, `[]`, etc).

As mentioned, the instantiation itself is not a type, so `LinkedList!i32*` is taking a `i32*`, **not** a pointer to a `LinkedList!i32`. (Another good reason for avoiding the use of `alias` in template instantiation expressions).

The underlying mechanism is simple. The compiler copies the parameters to where they are used, and a struct of the name given in the template instantiation expression is generated in the underlying IR. *The backend has no knowledge of templates, and the result is as if the user had typed in the resulting type themselves.*

#Mixin

When instantiating a template, you can optionally give the `mixin` keyword.

    struct Instance = mixin Definition!(i32);

What this keyword (or the lack thereof) determines is the context in which the template is instantiated. If the `mixin` keyword is given, the context is at the point of instantiation. For example, if we define a template that calls a library function.

    struct Definition!(T)
    {
        fn foo()
        {
            writeln("hello");
        }
    }

If we instantiate that without `mixin`

    import watt.io;
    struct Instance = Definition!i32;

It won't work: the template will not 'see' the import. But if we add `mixin`

    import watt.io;
    struct Instance = mixin Definition!i32;

It will work. Think of `mixin` like copy and paste.

#Functions

As noted, `struct`, `union`, `interface`, `fn`, and `class`, are the types available for templates.

    fn add!(T)(a: T, b: T) T
    {
        return a + b;
    }
    
    fn integerAdd = add!i32;
    
    ...
    return integerAdd(40, 2);

#Notes

If there is one template argument or parameter, the parens can be omitted, both in template definitions and instances:

    fn add!T(a: T, b: T) T { return a + b; }
	alias intadd = add!i32;

#Major Revision 1: Value Arguments

The above limitations were never going considered to be set in stone, and value arguments to templates is useful enough, and simple enough (both in terms of user understanding and implementation) that they seem like a very low hanging fruit.

The template definition is very easy to add values to.

    struct Math!(pi: f64, T)
    {
        ...
    }

The above declares a templated struct definition Math that takes the value of Pi (hey, it might change), and a type T, as usual. The value (`pi`, in this case) can be used anywhere you'd use a constant. And can't be used where you couldn't use a constant. For instance, you can't take the address of `pi` -- it's not an lvalue.

The definition is trivial, too.

    struct MyMath = mixin Math!(3.1415926538, f64);

The arguments to a value argument must be known at compile time. Rule of thumb: if you couldn't give it to an enum as a value, you can't use it in a template.
