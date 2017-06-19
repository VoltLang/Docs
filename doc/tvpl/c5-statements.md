---
title: Chapter 5 - Statements
layout: page
---
# Chapter 5 - Statements

Statements manage the flow of code execution.

## BlockStatement

We've seen the block statement several times already. It's attached to functions we write, `foreach`, `if`, and many others. They group other statements together, and introduce a new scope. Inside a function, we can just add a block statement on its own, if we want the ability to use the same variable name for different variables. If you find yourself doing this, it's probably a sign to split your function into more functions, but it's there if you need it.

	import watt.io;
	
	fn main() i32
	{
		{
			x := 2;
			writeln(x);
		}
		{
			x := "hello";
			writeln(x);
		}
		return 0;
	}

Output:

	2
	hello

## Return Statement

We covered the `return` statement briefly; it stops execution of a function and returns a value to the caller. If a function has a void return value, you can use `return;` to return execution from that function.

	import watt.io;
	
	fn foo(n: i32)
	{
		if (n < 100) {
			return;
		}
		writeln("big n");
	}

	fn main() i32
	{
		foo(101);
		foo(50);
		return 0;
	}

Output:

	big n

## Assert Statement

The `assert` statement verifies a condition. If the condition is false, the execution of the program is halted. An optional message of your own can be supplied to be displayed. Use this for things that should not happen; they help point out bugs earlier. In general, the earlier a bug is found, the easier it is to fix. So use them liberally. Depending on the compiler, these may or may not be included in release builds, so never rely on them for error handling.

	fn main() i32
	{
		assert(true, "this assert will not trigger");
		assert(5 > 10, "but this one will");
		return 0;
	}

## If Statement

We covered this earlier, so here we'll mention something that's not entirely obvious. The 'truthiness' of an `if` statement is slightly more permissive than just assigning to a `bool` variable. Along with `true` and `false`, non zero integers are considered `true`, while zero is `false`.

## While Statement

Like a `foreach` statement, `while` executes code in a loop. Like an `if` statement, it only evaluates one condition.

	import watt.io;
	
	fn main() i32
	{
		i := 0;
		while (i < 3) {
			writeln(i++);
		}
		return 0;
	}

Output:

	0
	1
	2

## Do While Statement

This is like a regular `while` statement, except the condition is checked at the end of a loop. So even a loop with a false condition is executed once. Not used a lot, but handy when you need it.

	import watt.io;
	
	fn main() i32
	{
		do {
			writeln("A");
		} while (false);
		return 0;
	}

Output:

	A

## For Statement

A `for` statement is another loop. It looks a little complicated at first, but you'll get used to it.

	for (<variables>; <condition>; <iterator>) {
	}

The variable is declared for the entirety of the `for` loop's block statement, the condition is checked to see if the loop is continued, just like a `while` loop. And the iterator is run after each loop.

	import watt.io;
	
	fn main() i32
	{
		for (i := 0; i < 5; ++i) {
			writeln(i);
		}
		return 0;
	}

Output:

	0
	1
	2
	3
	4

All of the sections are optional. If the condition is empty, it is considered to always be true. So a common way of writing an infinite loop (a loop that never terminates) is then:

	for (;;) {
	}

## Foreach Statement

We've been introduced to the `foreach` statement. Here's a different form that is often useful:

	import watt.io;
	
	fn main() i32
	{
		foreach (i; 0 .. 5) {
			writeln(i);
		}
		return 0;
	}

Output:

	0
	1
	2
	3
	4

If you declare the foreach variable as `ref`, if you modifiy the iteration variable, you'll modify what you're iterating over.

	import watt.io;
	
	fn main() i32
	{
		a := [2];
		foreach (ref i; a) {
			i *= 3;
		}
		writeln(a[0]);
		return 0;
	}

Output:

	6

## Switch Statement

A `switch` statement runs code depending on its value. Similar to several `if` `else if` chains, but more compact.

	import watt.io;
	
	fn main() i32
	{
		a := 3;
		switch (a) {
		case 1, 2:
			writeln("one or two");
			break;
		case 3:
			writeln("three");
			break;
		default:
			writeln("default");
			break;
		}
		return  0;
	}

Output:

	three

If the `break` isn't present in a non-empty case, the compiler will error. This is because in C, a switch statement would 'fall-through' to the next case if `break` was not present. If you wish to jump to the next case, use `goto case;` instead of `break;`. An empty case is special cased to fall-through quietly.

	case 1:
	case 2:
	case 3:
		// This hits cases 1 and 2 as well.

## Continue Statement

The continue statement can only occur inside of a loop. When it is run, the current loop starts from the beginning, if its condition is true. Otherwise, the loop is exited.

	import watt.io;
	
	fn main() i32
	{
		i := 0;
		while (i < 10) {
			if (++i <= 9) {
				continue;
			}
			writeln(i);
		}
		return 0;
	}

Output:

	10

## Break Statement

We've seen the `break` statement breaking out of `switch` `case`s, but it works in loops too. It breaks out of the current loop, regardless of the its condition.

	import watt.io;
	
	fn main() i32
	{
		do {
			writeln("A");
			break;
		} while (true);
		return 0;
	}

Output:

	A

## With Statement

The `with` statement takes an expression that is checked first when doing lookups. This can make some code easier to read.

	arr := [1, 2, 3];
	with (arr) {
		return length;  // Same as arr.length.
	}

## Synchronized Statement

The `synchronized` statement ensures that only one thread can execute in its block. If that's meaningless to you, it'll make more sense once you learn about threads.

	synchronized {
		foo();
	}

## Throw Statement

The `throw` statement takes an instance of any object inheriting from `core.exception.Throwable`. The stack is then 'unwound' (control is passed back to the function that called the current function) until it finds a `catch` statement that matches, or if it can't, the Volt runtime will kill the entire process. This is for errors that are expected to happen. For instance, if a file cannot be opened, the file opening function might throw a `FileNotFoundException`.

## Try/Catch/Finally

If an exception is thrown by code in the `try` statement's block, its attached `catch` statements are checked to see if they match the thrown Exception, and if they do, control is passed to them. If there is a `finally` block attached to the `try` statement, it receives control last, regardless what exception was caught.

	import watt.io;
	
	import core.exception;
	
	fn main() i32
	{
		try {
			throw new Exception("");
		} catch (e: Exception) {
			writeln("caught!");
		} finally {
			writeln("finally!");
		}
		return 0;
	}

Output:

	caught!
	finally!

# Scope Statement

The `scope` statement is executed when a function is left. `scope (success)` is run when a function is exited via a `return` statement. `scope (failure)` is run when a function is exited via a `throw` statement. And `scope (exit)` is always run, no matter how the function was left.

	import watt.io;
	
	fn main() i32
	{
		scope (exit) {
			writeln("bye!");
		}
		writeln("hi!");
		return 0;
	}

Output:

	hi!
	bye!
