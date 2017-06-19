---
title: Chapter 7 - User Defined Types
layout: page
---
# Chapter 7 - User Defined Types

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

# Wrapping Up

[Next, some parting remarks and some useful tools.](conclusion.html)
