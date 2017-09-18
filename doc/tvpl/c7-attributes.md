---
title: Chapter 7 - Attributes
layout: page
---
# Chapter 7 - Attributes

This chapter will briefly go over some of the attributes you can use when writing programs.

## Attributes

An attribute acts like a tag that attaches itself to other pieces of code. There are several ways of applying an attribute to code (don't worry about what `private` means at the moment, we'll get to that, but for now it is enough to know that it is an attribute).

To apply an attribute to a single declaration, simply write the keyword before the declaration itself:

    private fn add(a: i32, b: i32) i32 { return a + b; }

You can apply an attribute to multiple declarations at once by using braces:

	private {
		fn func1() {}  // This is marked private.
		fn func2() {}  // This too.
	}
	fn func3() {} // But this isn't.

You can apply an attribute to all following declarations at the same level by using a colon:

	struct S
	{
	private:
		fn func1() {} // Private.
		fn func2() {} // Private.
	}
	fn func3() {} // Not private.

Note that some attributes are the opposite of other attributes, and you can 'turn off' these attributes by using their opposites:

	private {
		fn func1() {} // Private.
		public func2() {} // Not private.
	}
	private:
	fn func3() {} // Private.
	public:
	fn func4() {} // Not private.

Now we have a basic understanding of how we can apply attributes, let's go over some of the things they can do.

## Access Levels

Volt defines three access level attributes: `public`, `private`, and `protected`. The access level determines how code that is not in the module a declaration is defined in can access a given declaration.

### Public

`public` is the default access level. If `private` or `protected` are never used, a declaration will be considered `public`. A `public` declaration can be used by any module that imports it.

### Private

`private` means that any code outside of the module where the `private` declaration lives will be unable to access it, and an attempt will generate an error at compile time.

	module a;
	fn add(a: i32, b: i32) i32
	{
		return addImplementation(a, b);  // We can use this because it's the same module.
	}
	private fn addImplementation(a: i32, b: i32) i32 { return a + b; }

And in another module:

	module b;
	import a;
	fn main() i32
	{
		v1 := add(10, 2);  // This is fine, 'add' is public.
		v2 := addImplementation(10, 2);  // error: tried to access private symbol addImplementation
	}

### Protected

`protected` is an access level that only occurs in `class` declarations (see [Chapter 8](c8-user-types.html)). To the outside world, `protected` behaves like `private`, in that it restricts access to `protected` symbols.

What makes it different is it allows child classes to see `protected` symbols. That is to say, if `class A` in `module a` defines a `private` method, `class B` in `module b` couldn't call that method, and neither could `fn bfunc` in `module b`. But if it defines a `protected` method, `class B` could call it, while `bfunc` could not.

## Access Levels And Overloading

Multiple functions with the same name, different arguments, but different access levels, cannot be overloaded.

	private fn foo() {}
	public fn foo(a: i32) {}  // error!

The only exceptions are constructors. This is because a `private` constructor is a way of 'disabling' a constructor.