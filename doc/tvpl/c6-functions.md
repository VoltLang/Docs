---
title: Chapter 6 - Functions
layout: page
---
# Chapter 6 - Functions

In this chapter we'll take a closer look at functions.

## Declarations

The compiler doesn't actually need the block statement (called the 'body') of a function to call that function.

	extern (C) fn printf(const(char)*, ...) i32;

A lot of that is new, but just focus on the semicolon (`;`) where the body (`{}`) would be. This tells the compiler that the function's code will be given later, and tells the linker (the program that makes a working executable out of the compiler's output) to wire it up. In this case, `printf`, a function provided by your systems C library (hence `extern (C)`).

Functions can also be declared *inside* other functions. These are known as 'inline functions', and can be very useful.

	import watt.io;
	
	fn main() i32
	{
		fn greet(name: string)
		{
			writefln("Hello %s.", name);
		}
		
		greet("Jill");
		greet("Bob");
		return 0;
	}

Output:

	Hello Jill
	Hello Bob

These functions can access variables declared above them (just like any other scope), and can be passed to other functions as *delegates*.

	import watt.io;

	fn callDg(dgt: scope dg())
	{
		dgt();
	}

	fn main() i32
	{
		i := 0;
		fn addOneToI()
		{
			i++;
		}
		callDg(addOneToI);
		writeln(i);
		return 0;
	}

Output:

	1

A *delegate* is a function with a context variable; in this case, the context variable contains the variables declared in `main`.

## Homogenous Variadics

	import watt.io;

	fn add(integers: i32[]...) i32
	{
		sum: i32;
		foreach (integer; integers) {
			sum += integer;
		}
		return sum;
	}

	fn main() i32
	{
		writeln(add(1, 2, 3));
		writeln(add([1, 2, 3]));
		return 0;
	}

Output:

	6
	6

If the last parameter of a function is an array that ends in `...`, the function is said to be a homogenous variadic function. The final array can be passed multiple values, and then it can treat it as a regular array, or an array can also be passed directly.

One restriction to note, is that `null` may not be passed as an argument in the variadic portion. This is because `null` is also a way of representing empty arrays, and the results may not be what you're expecting. If you want an empty array, simply don't pass any variadic arguments, and if you want a `null` argument, simply `cast` it:

	fn foo(arr: MyObject[]...) { ... }
	foo(cast(MyObject)null);

## Acceptable Ref and Out Parameters

	fn plusOne(ref i: i32)
	{
		i = i + 1;
	}

If the above function is called, it has to be called with an 'l-value' as the first parameter. An 'l-value' is a value that could go on the left in an assignment expression -- something you can take the address of.

That's because, if you call it like this:

	i := 0;
	plusOne(ref i);

It's as if the code was:

	fn plusOne(i: i32*)
	{
		*i = *i + 1;
	}
	...
	i := 0;
	plusOne(&i);



## A Note On Void Return Types

	fn foo() void;
	fn foo();

The above two declarations are identical, the second is shorthand for the first.

## Function Overloading

Multiple functions in the same module can share the same name, if their parameters are sufficiently different to distinguish them from one another.

	fn print(val: string) { ... }
	fn print(val: i32) { ... }
	fn print() { ... }

	fn main() i32
	{
		print(32);  // Call the second function.
		print();  // Call the third function.
		print("hello");  // Call the first function.
		return 0;
	}

Functions can't be overloaded by return type. Note that overloading is checked at the call point, so you can define multiple functions that have the same parameters but different return types...

	fn foo() i32 { return 0; }
	fn foo() bool { return true; }

The error will only occur once someone tries to *call* those functions:

	foo();  // error: two functions match call
