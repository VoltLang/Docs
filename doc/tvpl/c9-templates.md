---
title: Chapter 9 - Templates
layout: page
---
# Chapter 9 Templates

Templates are a way of reusing one piece of code, using different types and constants.

Using a template requires two parts: a template definition, and a template instance. This is a one:many relationship; one template definition may be the code behind multiple template instances.

Templates are supported for functions, structs, classes, unions, and interfaces.

## Aggregates

Let's say we wrote a simple linked list for `i32`s:

```volt
struct I32List
{
	data: i32;
	next: I32List*;

	// Imagine a lot of code here.
}
```

But if we want to add a list that works on `f32`s, without templates, we're stuck maybe adding an extra `data` field, or copy pasting and making an `F32List`. Neither of these are great. Enter templates!

```volt
struct List!(T)
{
	data: T;
	next: List*;

	// Imagine a lot of code here, too.
}
```

The above is a template definition. We can't do anything with that on its own. To use it, we use template instances:

```volt
struct I32List = mixin List!i32;
struct F32List = mixin List!f32;
```

There's a lot to unpack here, but before getting bogged down in details, lets touch on the *results* of this code. The result is we have two `struct`s defined, that we can use like a normal `struct`: `I32List`, and `F32List`. The `T` in the template definition is replaced by `i32` and `f32` respectively. The use of the definition name, `List`, in the `next` field is replaced by `I32List` and `F32List` respectively. The `mixin` means that it is instantiated in the scope where the instances appears, not where the definition exists. This is relevant for import lookups -- a non mixin template would need any needed imports at the definition site, a mixin template at the instantiation. Currently, only mixin templates are implemented.

The `!(T)` portion of the template definition is a list of types and values that the instances have to give. Here's a more complicated one:

```volt
struct DataEntry(Key, Value, MaxSize: i32)
{
	k: Key;
	v: Value;

	fn getMax() i32
	{
		return MaxSize;
	}
}

struct OurDataEntry = mixin DataEntry!(i32, string, 32);
```

As you can see, when passing arguments to a template instance, if there is only one parameter (like in our `List` example) we can omit the parens, otherwise parens and commas are used to separate arguments.

If a parameter is just a word (like `T`, `Key`, `Value`) then it is to be replaced by a type given at the template instance. If it is of the form `word: type` (like `MaxSize: i32`) then it is a constant value to be given at the template instance.

All the parameters for a given instance are available from outside of the struct, too:

```volt
max := OurDataEntry.Max;
v: OurDataEntry.Key;
```

Using templates with classes, unions, or interfaces is just like we did there, except use `class`, `union`, or `interface` respectively instead of `struct`, for both the instantiation and definition.

## Functions

The other type of template are standalone functions. There's nothing really different here in how you use them.

```
fn add!(T)(a: T, b: T) T
{
	return cast(T)(a + b);
}

fn addInteger = add!i32;
fn addFloat   = add!f32;
```

## Static Ifs and Is Expressions

These aren't technically templates, but templates are where they're most useful. `static if` are like regular `if` statements, but they are evaluated at compile time. This means that the condition must be known at compile time, and evaluate to a boolean.

```
fn main() i32
{
	static if (1 + 1 == 2) {
		return 3;
	} else {
		return 2;  // This code is not compiled at all.
	}
}
```

`is` expressions allow you to query information about a type, at compile time.

```volt
static if (is(string == immutable(char)[])) {   // Evaluates to `true`.
	...
}
```

This allows you to change the way functions in templates behave, depending on the type. The above is the simple equality comparison of the `is` expression -- 'is the type on the left, the type on the right?'.

### Trait Words

These are special words that can go on the right hand side of `is` expressions, after an `@` symbol.

```volt
is(T == @isBitsType)   // Is T i8, i16, i32, i64, u8, u16, u32, u64, f32, or f64?
is(T == @isArray)      // Is T an array?
is(T == @isConst)      // Is T const?
is(T == @isImmutable)  // Is T immutable?
is(T == @isScope)      // Is T scope?
```

### Trait Modifiers

Like trait words, but they go on the left hand side of `is` expressions, to specify a property of the type to check.

```volt
/* Is the element of T const? If T is not an array, or the element isn't
 * const, this would be true.
 * e.g. (if T is char, false. If T is char[], false. If T is const(char)[], true.
 * If T is const(char[]), true.
 */
is(@elementOf!T == @isConst)
// Is the key of T an i32? False if T is not an AA.
is(@keyOf!T == i32)
// Is the value of T an i32? False if T is not an AA.
is(@valueOf!T == i32)
// Is the base of T an f32? False if T is not a pointer.
is(@baseOf!T == f32)
```

There's no `!=` in an `is` expression -- use `!is(...)` instead.

# Wrapping Up

Next, some parting remarks and some useful tools.

---

[PREV](c8-user-types.html) [INDEX](c1-intro.html) [NEXT](conclusion.html)