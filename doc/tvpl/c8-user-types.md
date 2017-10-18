---
title: Chapter 8 - User Defined Types
layout: page
---
# Chapter 8 - User Defined Types

Volt has several facilities for users to create more complex custom types.

## Structs

A struct is a bunch of variables bundled with functions that operate on those variables.

	import watt.io;

	struct Person
	{
		age: i32;
		name: string;
		
		fn introduceSelf()
		{
			writefln("Hello, I'm %s and I'm %s years old.", name, age);
		}
	}

	fn main() i32
	{
		cindy: Person;
		cindy.name = "Cindy";
		cindy.age = 22;
		cindy.introduceSelf();
		return 0;
	}

Output:

	Hello, I'm Cindy and I'm 22 years old.

There's not a lot more to it. They can be passed around, assigned to, just like a primitive type. Note that structs are 'value' types -- if you pass one into a function without using pointers or `ref`, and modify the fields, the `struct` is copied, and the caller doesn't see anything change.

	import watt.io;
	
	struct S
	{
		x: i32;
	}
	
	fn twiddle(s: S)
	{
		s.x = 12;
	}
	
	fn main() i32
	{
		s: S;
		s.x = 6;
		twiddle(s);
		writeln(s.x);  // Not 12!
		return 0;
	}

Output:

	6

## Unions

Unions are identical to structs in most ways and declared in an identical fashion.

	struct S
	{
		a: i32;
		b: i64;
	}

	union U
	{
		a: i32;
		b: i64;
	}

If we were to layout `S` in memory, it would look like this:

	[a _ _ _][b _ _ _ _ _ _ _]

(Depending on your system, there may be padding between `a` and `b`. That's not important right now.) But if we were to show `U`:

	[b[a _ _ _] _ _ _ _]

All the fields on a union are in the same piece of memory. So the size of a `struct` with 10 `i8` fields will be 10 bytes, but a `union` with the same 10 `i8` fields will be 1 bytes in size. Assigning to one field will change the others. This can be useful if you have a type that is in one of several exclusive configurations, but you want to save on memory.

## Classes

A full description and tutorial on object oriented programming is beyond this documentation's scope. If you're interested in learning more, [the wiki](https://en.wikipedia.org/wiki/Object-oriented_programming) page on it is a good place to start. That said, the simple utility of classes should make itself apparent.

	import watt.io;
	
	class Person
	{
		fn sayHello()
		{
			writeln("I am a person. Hi!");
		}
	}

	class Doctor : Person
	{
		override fn sayHello()
		{
			writeln("I am a person who is also a doctor. Hi!");
		}
	}

	fn main() i32
	{
		p1: Person = new Person();
		p2: Person = new Doctor();
		p1.sayHello();
		p2.sayHello();
		return 0;
	}

Output:

	I am a person. Hi!
	I am a person who is also a doctor. Hi!

`Doctor` is said to 'inherit' from `Person`: it is a 'child class'.

You will note that `p2` is declared as a `Person`, not a `Doctor`, but because it was initialised with a `Doctor` object, the `Doctor` greeting is used when `sayHello` is called. Class functions are known as 'methods', and can be 'overriden'. So if you have a `Person` object, you don't know exactly what it's going to do when you call `sayHello`. It might use the regular greeting, or a different one.

Unlike structs, classes are always 'reference' types.

	p: Person;
	p.sayHello();  // This crashes as p is null.

The default value of an object is `null`. Trying to call a function or look up a variable of a null object will cause a crash.

We can give values to objects when they're being constructed by defining a 'constructor'.

	import watt.io;
	
	class IntDoubler
	{
		x: i32;
		
		this(x: i32)
		{
			this.x = x * 2;
		}
	}

	fn main() i32
	{
		//id := new IntDoubler();  // Error: expected i32 argument
		id := new IntDoubler(32);
		writeln(id.x);
		return 0;
	}

Output:

	64

## Interfaces

A class can only inherit from one parent. An interface is a series of methods that a class can implelement so that it can be treated as an 'instance' of those interfaces.

	import watt.io;
	
	interface IntGetter
	{
		fn getInt() i32;
	}
	
	interface StringGetter
	{
		fn getString() string;
	}

	class TheClass : IntGetter, StringGetter
	{
		override fn getInt() i32
		{
			return 1;
		}
		
		override fn getString() string
		{
			return "watermelon";
		}
	}
	
	fn doInt(ig: IntGetter)
	{
		writeln(ig.getInt());
	}
	
	fn doString(sg: StringGetter)
	{
		writeln(sg.getString());
	}
	
	fn main() i32
	{
		tc := new TheClass();
		doInt(tc);
		doString(tc);
		return 0;
	}

Output:

	1
	watermelon

The `doInt` and `doString` functions do not need to know that the class `TheClass` exists. It implements the `interface` it cares about, and that's good enough. This allows you to compartmentalise your class designs.

## Operator Overloading

When working with builtin types, we can use natural 'operators', like `+` to perform addition, or `[3]` to look up the third item in an array. It can be useful to make our classes and structs use the same operators; `obj + obj` can be more succinct than `obj.add(obj)`.

To implement these operators, we define a member function with a name corresponding to the operator we wish to overload. When our type is involved in an operation with an operator, the compiler will check to see if the function for that operation has been defined. If it has, it will rewrite the expression to call your code. A simple example:

	struct Point2D
	{
		x, y: i32;
		fn opAdd(p: Point2D) Point2D
		{
			result: Point2D;
			result.x = this.x + p.x;
			result.y = this.y + p.y;
			return result;
		}
	}

	fn main() i32
	{
		a, b: Point2D;
		a.x = 12; b.x = 6;
		c := a + b;  // c is a Point2D with an x of 18 and a y of 0.
		return 0;
	}

Now we will go over the operators that can be overloaded, and the function name to use. If the type is unspecified, then it can be most anything -- the same type, a primitive, etc: whatever makes sense for that object and operator. If the return type of a function is not explicitly mentioned, it can be anything too, not just `void`.

### Binary Operator Overloads

`==` uses `fn opEquals(a) bool`, where `a` is the right side of the equation.

`+` uses `fn opAdd(a)`, where `a` is the right side of the equation.

`-` uses `fn opSub(a)`, where `a` is the right side of the equation.

`*` uses `fn opMul(a)`, where `a` is the right side of the equation.

`/` uses `fn opDiv(a)`, where `a` is the right side of the equation.

`+=` uses `fn opAddAssign(a)`, where `a` is the right side of the equation.

`-=` uses `fn opSubAssign(a)`, where `a` is the right side of the equation.

`*=` uses `fn opMulAssign(a)`, where `a` is the right side of the equation.

`/=` uses `fn opDivAssign(a)`, where `a` is the right side of the equation.

`>=`, `>`, `<`, and `<=` all use `fn opCmp(a) i32`, where `a` is the right side of the equation, and the result is less than 0 if the object that opCmp belongs to is less than the argument, more than 1 if it's greater, and 0 in all other cases.

### Postfix Operator Overloads

`[]` uses `fn opIndex(a)` where `a` is the value that is in the brackets, indexing the object.

`[a .. b]` uses `fn opSlice(a, b)` where `a` is the left portion of the slice, and `b` is the right portion.

If you're assigning to any of the above two (e.g. you have `opIndex` defined and you do `s[0] = 3`) then Volt will look for `opIndexAssign` and `opSliceAssign`, which will take the right hand side of the assign as their last argument. Their return types must match the `opIndex` or `opSlice` function. Assign operators like `+=` or `-=` will be rewritten so that the last argument is what you'd expect.

e.g. `s[0] -= 5` is rewritten to `s.opIndexAssign(0, s.opIndex(0) - 5)`.

`$`, while not strictly a postfix operator, only occurs in index operations, and can be overloaded using `fn opDollar()`.

### Unary Operator Overloads

`-` uses `fn opNeg()`. Note that this is the unary minus (`a := -obj;`), not the binary subtraction operator.

# Wrapping Up

Next, some parting remarks and some useful tools.

---

[PREV](c8-user-types.html) [NEXT](conclusion.html)
