---
title: Ideas for Volt
layout: page
---

New syntax
---
The idea is that when we are introducing symbols into a context you always start with a keyword followed by an identifier. Though it's debatable if requiring `var` for all variables is desirable.

```
// As it is now:
int x;
int func1() {}
alias al = int;
import io = watt.io;
class Class {}
struct Struct {}

// New unified syntax:
var x : int;
fn func1() int {}
alias al = int;
import io = watt.io;
class Class {}
struct Struct {}
```

New integer types
---
Make the builtin types be on a more uniform form. Bool should be left as is. Rust has the most compact types and also easiest to write. Linux kerenl looks a lot like rust except it has `s8` instead of `i8`.

```
// Volt/D
 byte  short  int  long index_t
ubyte ushort uint ulong size_t

// Rust
i8 i16 i32 i64 isize
u8 u16 u32 u64 usize
       f32 f64

// Go
int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr
float32 float64
```

Optional GC
---
Adding an option for the compiler to change the default for functions to `@nogc`. Make sure we can compile the runtime and watt with this option enabled.

Explicit this
---
Explicit `this` arguments to member functions. It also removes the need to tag functions with `static`, `global` or `local`.

```
// As it is now:
int func1(int arg) {...}
int func2(int arg) const {...}

// With explicit this:
int func1(this, int arg) {...}
int func2(const this, int arg) {...}

// Different syntax:
fn func1(this, int arg) int {...}
fn func2(const this, int arg) int {...}
```

Better reference control
---
Building on top of the explicit `this` idea we can now control if this is a copy or a reference. For classes, references would be required.  Maybe we should move away from `ref` to `&` for brevity.

```
fn func1(ref this, int arg) int {...}
// vs
fn func2(this&, int arg) int {...}
```

Builtin string
---
Making `string` be a builtin type allows for several optimizations to be made. Since string is immutable it allows us to cache the hash for the string. The string length is now in the `object` instead, leading to less memory usage at the referee site. It allows for transparent [interning](https://en.wikipedia.org/wiki/String_interning) of strings, this gives two benefits. One, equality becomes a pointer compare. And two, applications that use many common strings (like a compiler) could see memory usage reductions.

```
struct __String
{
	size_t hash;
	size_t length;
	// Followed by length + 1 bytes of string data.
	// Inbuilt strings are always zero terminated.
}
```

Building reference counting strings on the regular gc string would be easy.

It would also be possible to implicitly cast the new string to `immutable(char)[]` and  `const(char)[]`.

Bitcast (aka recast)
---
In both Volt and D cast is a semantical smart cast. But sometimes you just want to take the bits you have and change their interpretation.

```
// Note 32 vs 64 bits
int foo = -1;
long minusOne = cast(long)foo; // Sign extend.
long uintMax = recast(long)foo; // No sign extend.

assert (minusOne == -1);
assert (uintMax == uint.max);

// Without recast:
long uintMax2 = cast(long)cast(uint)foo;
```

This opens up a bit of a rabbit hole, certain casts should only be recasts and not semantic casts: to and from pointers and integers; to and from signed and unsigned. Should we error on these and require the use of recast? (hint: yes)

