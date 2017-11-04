---
title: Chapter 3 - First Steps
layout: page
---
# Chapter 3 - First Steps

This chapter will be a whirlwind tour of a lot of features. We'll go into more depth next chapter.

## Displaying Values

For now it's enough to know that numbers can be given to `writeln` too.

	import watt.io;

	fn main() i32
	{
		writeln(42);
		return 0;
	}

Should output `42` onto your console. Don't worry about the `return 0` for now.

## Variables

Variables hold values. Numbers like the `42`, or 'strings' of text, like `hello, world` from the last chapter. In the simplest cases, defining a variable is easy. Give it a name and a value you want to associate with it, separated by `:=`.

	import watt.io;

	fn main() i32
	{
		numberOfMonths := 12;
		firstMonth := "January";
		writeln(numberOfMonths);
		writeln(firstMonth);
		return 0;
	}

Will output:

	12
	January

This is useful enough, but as their name implies, we can change a variable's contents.

	n := 1;
	writeln(n);
	n = 2;
	writeln(n);

Output:

	1
	2

Note that the first time we *define* the variable, we use the type inference operator `:=`. This asks the compiler to create a new variable of the name before the `:=`, and assign it the given value. `=` on its own is the 'assignment operator'. This asks the compiler to assign a value to an already defined variable. Variables can't be given the same name. It's slightly more complicated than that, in reality, but that will come later.

The above is a shorthand. Volt is a 'statically typed' language; variables and expressions always have a 'type'. `3` is an integer, `"hello"` is a `string`, and so on. All variables have a type, and it cannot be changed.

	n := 1;
	n = "hello";   // ERROR: can't convert string to an integer.
	n := "hello";  // ERROR: variable 'n' is already defined.

What if we want to declare a variable, but we don't have a value to assign to it right away? How do we give it a type without using `:=`? Well, A long form version of the example from the beginning of the section is as follows:

	import watt.io;

	fn main() i32
	{
		numberOfMonths: i32 = 12;
		firstMonth: string = "January";
		writeln(numberOfMonths);
		writeln(firstMonth);
		return 0;
	}

The output of the program is the same. It's just a little more typing. With this method you can define a variable, and assign to it later:

	n: i32;
	// ...
	n = 2;

We'll talk more about types later on. For now, it is enough to know that they exist, and that for most of our examples we will be using integers, represented in Volt as `i32`.

## Maths!

	import watt.io;

	fn main() i32
	{
		writeln(1 + 1);
		return 0;
	}

Output:

	2

Numbers on their own aren't that useful. Volt can also do subtraction:

	0 - 5  // output: -5

Multiplication:

	5 * 2  // output: 10

Division:

	4 / 2  // output: 2

This one deserves a little elaboration. Integers are whole numbers. We'll touch on 'real' numbers later, but be aware that the above is 'integer division'. Any fractional portion is ignored:

	5 / 2  // output: 2, NOT 2.5

If you know anything about maths you'll know that dividing by zero is not possible. So what happens if we try it?

	8 / 0

This program has no output; the OS will kill the process for performing an illegal operation.

You can also mix expressions with numbers, and variables that hold numbers:

	n := 3
	n - 2  // output: 1

There are many more expressions, which we will go over in the next chapter.

## Arrays

Say we wanted to store the names of the months. We could do it with individual variables:

	firstMonth := "January";
	secondMonth := "February";
	...
	twelthMonth := "December";

This works, but there's a more elegant way. Use an 'array':

	months := ["January", "February", "March", "April", "May",
		"June", "July", "August", "September", "October", "November", "December"]
	writeln(months[0]);
	writeln(months[11]);
	writeln(months[1+1]);

Output:

	January
	December
	March

An array contains multiple values in a single variable. The above would be called "an array of strings". Arrays are a list of values with the same type. We can look up one of the values with an 'index expression'. The numbering of the index expressions start from zero, which is why `months[1]` would be `February` and *not* `January`. You can create an array that contains no values with the 'new' expression:

	months := new string[](12);
	months[0] = "January";
	...

The `12` in the example above is the length of the array; how many values it holds. You can get this value by using the `.length` property of an array:

	writeln(months.length);  // output:12

## Strings

Strings are series of characters. You've used these already. Strings like `"January"`, `"Hello World"`, and `"こんにちは、世界"` are simple; you see what they are. There are ways of making more complicated strings, however.

### Composable Strings

Composable strings are a special kind of string that allows you to easily display the results of expression.

	writeln("${2*3}");  // output:6

A composable string looks like a regular string, except it contains a composable string component -- an expression, wrapped in `${` and `}`. In a regular composable string, everything has to be known at compile time. A constant, in other words.

However, it is very useful to be able to display runtime values. If you precede a composable string with `new`, you can display non-constant values. The `new` is to remind you that this isn't free -- it will allocate memory, and call into runtime functions. A composable string without `new` is identical to a regular string literal at the end of compilation, no runtime overhead occurs.

	fn main(args: string[]) i32
	{
		writeln(new "${args.length * 2}");
		return 0;
	}

Composable strings can format arrays, associative arrays, `union`s, `struct`s, `class`es, `enum`s, primitive types (`i32`, `f32`, etc), and pointers. Trying to process anything else in a composable string component is an error. Most of the way things are formatted are as you'd expect.

	"${3+2}"          // "5"
	"${Enum.Member}"  // "Member"
	new "${[1, 2, 3]}"// "[1, 2, 3]"

But special note should be made of `union`, `struct`, and `class`es. By default, they'll just display the name of the type. But if a `toString` function is defined, that takes no arguments, and returns a string, then that function will be called and the result will be used.

	struct A {}
	struct B { fn toString() string { return "hello"; }
	a: A;
	b: B;
	astr := new "${a}";  // "A";
	bstr := new "${b}";  // "hello";

## Foreach Statement

So far the programs we've written have all been very linear. They start at the top, and run all our code until they get to the end. What if we took our previous example, and wanted to display all the months. We *could* do something like this:

	import watt.io;

	fn main() i32
	{
		months := ["January", "February", "March", "April", "May",
			"June", "July", "August", "September", "October", "November", "December"];
		writeln(months[0]);
		writeln(months[1]);
		...
		writeln(months[11]);
		return 0;
	}

But there's an easier way. Statements are language features that do things. In this case, the `foreach` runs some code **for** **each** entry in a list.

	import watt.io;

	fn main() i32
	{
		months := ["January", "February", "March", "April", "May",
			"June", "July", "August", "September", "October", "November", "December"];
		foreach (month; months) {
			writeln(month);
		}
		return 0;
	}

Output:

	January
	February
	...
	December

This runs `writeln(month)` twelve times; once for every element in the array 'months'. We can also print out what iteration it's on too:

	foreach (i, month; months) {
		writeln(i);
		writeln(month);
	}

Output:

	0 January
	1 February
	...
	11 December

Notice that like our `main` function, the `foreach` statement has `{` and `}`. These group statements together. They also affect how variables are looked up, a topic which we'll get into in a few sections.

## If Statement

	import watt.io;

	fn main() i32
	{
		a := 10;
		b := 100;
		if (a > b) {
			writeln("a is bigger than b");
		} else {
			writeln("a is not bigger than b");
		}
		return 0;
	}

Output:

	a is not bigger than b

The if statement checks if a condition is true. If it is, it performs a group of code. Otherwise, it passes control to an `else` statement. The else statement is optional:

	if (a > b) {
		writeln("this is not printed");
	}
	writeln("but this always will be");

Output:

	but this always will be

## Functions

Functions are a block of code that can be 'called' to do a thing by other pieces of code.

	import watt.io;

	fn sayHello()
	{
		writeln("Hello");
	}

	fn main() i32
	{
		sayHello();
		sayHello();
		sayHello();
		return 0;
	}

Output:

	Hello
	Hello
	Hello

The keyword `fn NAME` declares a function with a given name. Most of our examples have featured a function: `main`. Main is a special function -- it's the first function called when your program is run.

Functions can optionally have return values. These go after the `()`. Our `main` function returns an integer: `i32`. You can store this value and use it like any other:

	import watt.io;

	fn getZero() i32
	{
		return 0;
	}

	fn main() i32
	{
		a := getZero();
		writeln(a);  // 0
		return 0;
	}

We can give values to functions to work with by defining a 'parameter list': a special list of variables that we give to the function when we call it.

	import watt.io;

	fn sayHello(name: string)
	{
		writeln("Hello there...");
		writeln(name);
	}

	fn main() i32
	{
		sayHello("Bob");
		sayHello("Jenny");
		return 0;
	}

Output:

	Hello there...
	Bob
	Hello there...
	Jenny

## Scope

Scope defines who can see which variables. If we make a variable outside of a function, it is known as a 'global' variable, and we even have to mark it as such:

	import watt.io;

	global n: i32 = 2;

	fn main() i32
	{
		writeln(n);  // 2
		return 0;
	}

A variable defined in a block statement is a 'local' variable, and can only be seen in that block statement, and block statements inside of that block statement.

	import watt.io;

	fn main() i32
	{
		a := 1;
		{
			writeln(a);  // 1
			b := 2;
			writeln(b);  // 2
		}
		writeln(b);  // ERROR: no variable 'b' defined.
		return 0;
	}

## Modules

All Volt code lives in 'modules' -- these correspond to `.volt` files. Our little example code hasn't done it here for the sake of space, but you should always name your modules:

	module a;

	global n: i32 = 5;

If we save that in a file `a.volt`, and then have `b.volt`:

	module b;

	import a;
	import watt.io;

	fn main() i32
	{
		writeln(n);
		return 0;
	}

If you then compile, passing both modules to *Volta*:

	volt a.volt b.volt

The output will be:

	5

You can use functions from imported modules too. In fact, `import watt.io;` imports a module from the *Watt* standard library (a 'library' is a collection of useful modules) that contains the `writeln` function we've been using to output values!

## Comments

Comments is text that is ignored by the compiler, where you can put notes for yourself, or other people that will read your code. Volt has three kinds of comments:

	// The single line comments applies from the '//' until the end of the line.

	/* The multiline comments apply from the '/*', until
	 * the closing sequences, which is that, but reversed.
	 */
	
	/+ Nested multiline comments are like the above, but
	 /+ they can be nested +/ this is still a comment. +/

## Getting Input

Getting input typed from the user will be useful for some of the examples in the next chapter. We can use the `readln` function, also found in `watt.io` to get it. When `readln` is called, the program will stop and wait for the user to input some text and then press enter. Once they do, any text that they have written will be returned in a string.

	import watt.io;

	fn main() i32
	{
		writeln("Please enter your name and press enter.");
		name := readln();
		writeln("Hello,");
		writeln(name);
		return 0;
	}

Output:

	Please enter your name and press enter.
	<name><enter>
	Hello,
	<name>

We can turn strings into numbers with the `toInt` function found in `watt.conv`:

	import watt.io;
	import watt.conv;

	fn main() i32
	{
		writeln("Enter a number.");
		n := toInt(readln());
		writeln("Your number doubled is");
		writeln(n * 2);
		return 0;
	}

Output:

	Enter a number.
	<number><enter>
	Your number doubled is
	<number times two>

If the user types a non-digit character, the program will crash. We'll cover how to handle errors more gracefully later.

---

[PREV](c2-setup.html) [INDEX](c1-intro.html) [NEXT](c4-types-and-expressions.html)