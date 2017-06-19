---
title: Chapter 4 - Types and Expressions
layout: page
---
# Chapter 4 - Types and Expressions

In this chapter we'll cover most of the types and the expressions that operate on them. Let's get started with the simple types; types that can exist on their own.

## Simple Types

### Integers

You've been introduced to one of the integers already; `i32`. The i stands for 'integer'. The number after the 'i' is how many bits - zeros and ones - represent the integer. That means the number determines the range of numbers that can be stored. For instance, the `i8` is 8 bits (1 byte) in size, and stores the integer range -128 to 127. If you add one to an `i8` holding a value of 127, it is said to 'overflow', and wraps around to -128. Here are all the signed integers:

	i8  min:-128 max:127
	i16 min:-32,768 max:32,767
	i32 min:−2,147,483,648 max:2,147,483,647
	i64 min:-9,223,372,036,854,775,808 max:9,223,372,036,854,775,807

As said, those are the 'signed' integers. The sign being the minus sign. There's another kind of integer - the 'unsigned' integer. As their name suggests, they don't have a sign; they can't hold negative values.

	u8  min:0 max:255
	u16 min:0 max:65,535
	u32 min:0 max:4,294,967,295
	u64 min:0 max:18,446,744,073,709,551,615

#### Integer Literals

A series of digits on their own is an integer literal:

	123
	46

If it's preceded by `-` it's a negative value:

	-4
	-42

Integer literals are signed by default, but if you put `U` on the end they will be given an unsigned type:

	32U
	56U

Underscores are ignored, this can help with larger numbers:

	1000_000_000  // same as '1000000000'
	3_2  // same as '32'

A constant starting with `0x` is a [hexadecimal](https://en.wikipedia.org/wiki/Hexadecimal) literal:

	0xFF  (same as 255)

A constant starting with `0b` is a [binary](https://en.wikipedia.org/wiki/Binary_number) literal:

	0b10  (same as 2)

### Floating Point

Floating points represent numbers with a decimal place. You can't represent pi as an integer, for instance, so that's where the floating point types come in. Volt has two, and like integers they are delimited by their size.

	f32
	f64

Floating points use the same arithmetic expressions as integers so `5.0 / 2.0` gives the result `2.5`. Floating point is a big topic, and the finer details deserve their own document. If you're interested, [What Every Computer Scientist Should Know About Floating-Point Arithmetic](https://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html) is well worth reading.

#### Floating Point Literals

The simplest form of floating point literal is just a number with a decimal point in it:

	0.0

Floating point literals are double precision (`f64`) unless they end with `f`:

	0.0f

### Characters

Character types hold little pieces of text. There are three:

	char   1 byte wide, for UTF-8
	wchar  2 bytes wide, for UTF-16
	dchar  4 bytes wide, for UTF-32

#### Character Literals

A character in single quotes (`'`) is a character literal:

	'a'
	'2'

The 'escape' character is a backslash (`\`), this lets you insert various special characters that you can't type:

	\' The ' character
	\\ The \ character
	\0 An ASCII nul (a zero)
	\n A newline character
	\r Carriage return
	\t A tab

### Remaining Primitives

The last two primitives are `bool` and `void`.

`bool` is a [boolean](https://en.wikipedia.org/wiki/Boolean_data_type) data type. It is either `true` or `false`.

`void` is not a type at all, but instead marks the absence of a type.

## Composite Types

These types can only be complete when paired with other composite types, and at least one concrete (a primitive, or user-defined) type.

### Arrays

We used these last chapter. To declare an array of type T, we would write `T[]`.

	i32[]     an array of i32s.
	bool[]    an array of bools.
	i16[][][] an array of an array of an array of i16s.

#### Array Literals

The `[` character denotes the start of the array literal. Values are separated by the `,` character, and the literal ends with the `]` character:

	[1, 2, 3]
	['a', 'b']

### Pointers

A [pointer](https://en.wikipedia.org/wiki/Pointer_(computer_programming)) holds the address of another value. To declare a pointer to type T, we would write `T*`.

	i32*     a pointer to an i32.
	bool[]*  a pointer to an array of bools.
	char*[]  an array of pointers to chars.

### Associative Arrays

An [associative array](https://en.wikipedia.org/wiki/Associative_array) associates 'keys' with 'values'. To declare a an associative array of T values, using J as a key, we would write `T[J]`.

	bool[i32]  an associative array of bools keyed by i32
		aa: bool[i32]
		aa[32] = false;
		writeln(aa[32])  // outputs 'false'
	void*[string]  an associative array of pointers to void keyed by string

#### Associative Array Literals

Like the array literals, associative array literals open with `[`, have values separated by `,`, and end with `]`. The values of an associative array literal have the key and value separated by a `:` character. So `[1:"hello", 2:"goodbye"]` would be a `string[i32]`, that has a key of "hello" associated with 1, and so on.

## Expressions

### Relational Operators

These return a `bool` value.

`==` is the equality operator. If the two sides of this expression are the same, it returns `true`. Otherwise, it returns `false`.

	import watt.io;
	
	fn main() i32
	{
		str := readln();
		if (str == "banana") {
			writeln("you wrote 'banana'");
		} else {
			writeln("you didn't write 'banana'");
		}
	}

`!=` is the inequality operator. It's like `==`, but instead returns `true` if both sides are *not* the same, otherwise it returns `false`.

	import watt.conv;
	import watt.io;
	
	fn main() i32
	{
		writeln("I'm thinking of a number between one and one hundred. What is it?");
		n := toInt(readln());
		if (n != 32) {
			writeln("you didn't get it!");
		} else {
			writeln("correct!");
		}
		return 0;
	}

The `<` and `>` and operators returns true if the left side is less than or greater than the right side, respectively.

	import watt.io;
	
	fn main() i32
	{
		if (5 > 2) {
			writeln("five is bigger than two");
		}
		if (2 < 5) {
			writeln("two is less than five");
		}
		return 0;
	}

Output:

	five is bigger than two
	two is less than five

The `<=` returns true if the left side is less than or equal to the right, and `>=` returns true if the right side is greater than or equal to the right.

	5 >= 4  false
	5 >= 5  true
	5 >= 6  true
	4 <= 5  true
	4 <= 4  true
	4 <= 3  false

### Logical Operators

`&&` returns true if both sides are true.

	true && true    true
	true && false   false
	false && false  false

`||` returns true if one or more side is true.

	true || true    true
	true || false   true
	false || true   true
	false || false  false

`!` returns true if it's applied to a false value, and false if it's applied to a true one.

	!true   false
	!false  true

### Casts

Usually, Volt won't let us assign a variable to another if the types aren't the same, unless it knows it'll fit. For instance,

	a: i32;
	b: i16;
	a := b;  // This is okay, as any i16 can fit into the larger i32
	b := a;  // Error: certain values of i32 may not fit into i16.

`cast` lets us say "we know what we're doing, assign anyway."

	import watt.io;
	
	fn main() i32
	{
		b: u8 = cast(u8)257;
		writeln(b);
		return 0;
	}

Output:

	1

As the maximum value that a `u8` can hold is a 255, the cast forces it to truncate the value, and it wraps around.

### Increment and Decrement

The increment and decrement operators increase and decrease a variables value by 1 each time.

	a := 0;
	a++;  // a is 1
	a--;  // a is 0

They can be used both before, and after the variable. These are known as prefix and postfix increment/decrement operators.

	a := 0;
	++a;  // a is 1
	a++;  // a is 2
	a--;  // a is 1
	--a;  // a is 0

There is a difference. The prefix operators return the value modified by the operation:

	a := 0;
	b := ++a;  // b is 1, a is 1

While the postfix operators return the value before modifying it:

	a := 0;
	b := a++;  // b is 0, a is 1

### Bitwise Operators

The bitwise operators perform logical operations on a bit level. See the [wikipedia page](https://en.wikipedia.org/wiki/Bitwise_operation) for more detail. These operations operate on types of the same size.

`|` sets a bit if one or both of the bits are set.

	0b0000 | 0b0001  // 0b0001
	0b0001 | 0b0001  // 0b0001
	0b0000 | 0b0000  // 0b0000

`&` sets a bit if both of the bits are set.

	0b0001 & 0b0001  // 0b0001
	0b0000 & 0b0001  // 0b0000
	0b0001 & 0b0000  // 0b0000

`^` sets a bit if one and only one of the bits are set.

	0b0000 | 0b0001  // 0b0001
	0b0001 | 0b0001  // 0b0000
	0b0000 | 0b0000  // 0b0001

`<<` shifts the bit pattern left by the number on the right.

	0b0001 << 1  // 0b0010
	0b0001 << 2  // 0b0100

`>>` shifts the bit pattern right by the number on the right.

	0b1000 >> 1  // 0b0100
	0b1000 >> 2  // 0b0010

## Assignment

In addition to the regular assignment operator we've been using, there are several that are combined with the operators we've been touching on in this chapter.

`a += 1` is the same as `a = a + 1`.

`a *= 1` is the same as `a = a * 1`.

`a /= 1` is the same as `a = a / 1`.

`a -= 1` is the same as `a = a - 1`.

## Ternary

The ternary expression is like a compact `if` statement. It returns a value dependent on a boolean expression.

	a := 5 > 2 ? "bigger" : "smaller";
	writeln(a);

Output:

	bigger

## Precedence

Operator precedence is the order in which operators (expressions) are evaluated. For instance, `*` has a higher precedence than `+`, so `5 * 5 + 2` is `12`, and not `35`. The associativity of an operator is from where groups are formed in expressions using the same precedence. `=` is right associative, so `a = b = c` is interpreted as `a = (b = c)`, not `(a = b) = c`. In order from highest precedence to lowest:

	* / %
	+ - ~
	<< >>
	< > <= >= in !in
	== != is !is
	&
	^
	|
	&&
	||
	= += *= etc

If you *wanted* `5 * 5 + 2` to mean `35`, you can wrap a part of an expression in parens to give it a higher priority, as parens are evaluated first: `5 * (5 + 2)`.
