---
title: CTFE in Volt
layout: page
---

CTFE in Volt
===
A `#run` expression runs a function at compile time.

Rules
---
A `#run` expression is a boundary. Only non mutably indirect data and arrays thereof can be sent through. At the time of writing, only top level functions may be called.

Function calls inside of the boundary may internally, and in calls to other functions, use mutably indirect types like classes and array of classes. One caveat is that `local` and `global` variables may not be read or assigned. Functions essentially need to be `pure`ish because they can mutate objects and arrays.

Implementation
---
The first implementation doesn't need to support Classes, Structs, or other named types. So we don't need to copy them. That leaves builtin types, let's focus on PrimitiveTypes and Arrays.

Example
---
Demonstrates all features, some not implemented yet.

```
void internalFunc(Clazz c, u32 f)
{
	if (f < 2) { return; }
	c.mutate();
	// Recursion works as expected in CTFE and run-time.
	internnalFunc(c, f - 1);
}

int[] ctfeFunc(u32 arg)
{
	c := new Class(arg);
	internalFunc(c, 4)
	return c.genArray();
}

enum Arr = #run ctfeFunc(6);

// Normal runtime function.
int[] func()
{
	c := new Clazz();
	c.setArray(Arr);

	// CTFE:able functions can be called at run-time as normal.
	internalFunc(c, 4);
	return c.genArray();
}
```

