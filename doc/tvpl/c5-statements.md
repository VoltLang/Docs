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

## Import Statement

We touched on `import` statements earlier in chapter 3, but there's more to them than `import watt.io`.  
At the top of this chapter we said that statements affect flow of execution. At first blush, this doesn't seem to be true of `import` statements; they don't *do* anything on their own, do they?

In fact, an `import` statement modifies every single lookup of an identifier in the module. 'Import' is perhaps a slight misnomer, then, as what the `import` statement does is add a scope (a place where names are stored) to the places the compiler looks when you type a name that isn't a keyword or builtin type.

### Simple Import

This is what we've been using.

    import mymodule;

Any `public` symbol that has a name that matches a lookup will be retrieved, unless a local declaration (one in the same module) exists by that name. Local declarations always trump something retrieved by import. In this way, importing code won't silently change behaviour.

### Alias Import

An alias `import` is like a simple one, but gives a list of symbols.

	import mymodule : a, b;

Only symbols included in the list after the colon will be considered. The aliases can also be renamed.

	import mymodule : newName = oldName;

The declaration `mymodule.oldName` is accessible, but only through `newName`. This is helpful to avoid name collisions, or simply to make code prettier.

### Bind Import

	import bindname = mymodule;

Now to use something inside of `mymodule`, you have to go through `bindname`:

	bindname.someFunction(12);

You can associate multiple modules with a single bindname by wrapping a list in `[` and `]`:

	import bindname = [modulea, moduleb];

Liberal use of bind imports is recommended, as it keeps dependencies obvious, code clean, and makes detecting unused `import`s very easy.

### Public Import

By default, `import` statements are `private` -- they only affect the module with the import statement. If you prepend an `import` statement with `public`, to anyone importing a module, it will be as if they also imported all of the public imports too.

	public import mymodule;

## Assert Statement

The `assert` statement verifies a condition. If the condition is false, the execution of the program is halted. An optional message of your own can be supplied to be displayed. Use this for things that should not happen; they help point out bugs earlier. In general, the earlier a bug is found, the easier it is to fix. So use them liberally. Depending on the compiler, these may or may not be included in release builds, so never rely on them for error handling.

	fn main() i32
	{
		assert(true, "this assert will not trigger");
		assert(5 > 10, "but this one will");
		return 0;
	}

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


## Implicit Casts To Bool In If, For, While, and Do While Statements.

The `if`, `for`, `while` and `do .. while` statements all share a common property: they each have an expression that dictates how they behave, depending on if that expression is `true` or `false`. To aid in code brevity, the implicit boolean cast behaviour in these expression differs to regular implicit rules.

These conversions fall into three broad categories.

### Primitive types.

An `i32`, for example. The rules here are simple. A value of `0` is considered `false`, anything else is considered `true`.

	if (integer) { ... }

Becomes equivalent to:

	if (integer != 0) { ... }

### Pointer and reference types.

Any kind of pointer, or an instance of a `class`. Again, the rules are simple. A value of `null` is considered `false`, anything else is considered `true`.

	if (ptr) { ... }

Becomes equivalent to:

	if (ptr !is null) { ... }

### Arrays

If an array's `length` parameter is greater than `0`, it is considered `true`. Otherwise, it is considered `false`. The value of `ptr` is entirely irrelevant to whether or not the array is considered `true` or `false`.

	if (array) { ... }

Becomes equivalent to:

	if (array.length > 0) { ... }

### Other

Anything else not mentioned above (other than `bool` expressions) will generated an error if used uncased in one of the afore mentioned statements.

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

## Reverse Foreach

`foreach_reverse` is like a regular `foreach`, except it goes from the end of a list to the beginning.

	import watt.io;

	fn main() i32
	{
		a := [1, 2, 3];
		foreach_reverse (i; a) {
			writeln(i);
		}
		return 0;
	}

Output:

	3
	2
	1

## Foreach Over Strings

"If you were to use the `foreach` statement to iterate over a `string`, what would happen?" To the novice, this may seem like a simple question. When we use `foreach` on, say, an `i32[]` variable, the element type is `i32`, and it goes over each element in the list one by one, with the index increasing by one every iteration. A `string` is an alias for `immutable(char)[]`, so foreach must use an element type of `char`, going over each character in the string. Right?

If `string` meant 'ASCII', you'd be right. However, a `string` is not 'ASCII'. In Volt, the string type is encoded as UTF-8. The readers that don't know what UTF-8 is, or what Unicode is, are strongly encouraged to read Joel Spolsky's classic article, [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets](http://www.joelonsoftware.com/articles/Unicode.html). If you work with computers in any capacity, the knowledge will be valuable to you.

Given the following piece of code:

    str: string = "Hello, world.";
	foreach (c; str) {
		writefln("%s", c);
	}

The question becomes: what do we do? Do we treat it like any other array, byte 0 = `'H'`, byte 1 = `'e'`, etc? In a lot of cases, that would seem to be the "obvious thing", but consider the following:

	str: string = "こんにちは、世界";
	foreach (c; str) {
		writefln("%s", c)
	}

The Japanese *kana* and *kanji* of the above string are represented by three bytes each in UTF-8, so simple iteration wouldn't work. Volt's runtime has code for determing how much a given index has to be increased to get to the next character. So another option is to call those functions automatically. Works for both examples, problem solved, right?

Not quite. Firstly there's an issue of philosophy: Volt likes to avoid hidden code wherever possible, and not everyone would be expecting that behaviour. More practically, there are cases where programs would want to iterate over a UTF-8 string byte by byte, and forcing a `cast(u8[])` in this case isn't ideal.

The solution Volt has opted for is to make you choose explicitly what behaviour you want. The above two code examples will generate an error, prompting you to explicitly choose the type of the iteration variable (that is, `c`).

    foreach (c: char; str)  - simple iteration
	
	foreach (c: dchar; str) - decode a UTF-8 character
	
	foreach_reverse (c: char; str) - simple backwards iteration
	
	foreach_reverse (c: dchar; str) - decode a UTF-8 character, starting from the last and going backwards.

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

---

[PREV](c4-types-and-expressions) [INDEX](c1-intro.html) [NEXT](c6-functions.html)