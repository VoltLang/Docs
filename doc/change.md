---
title: Stuff to change.
layout: page
---

Changes
===

Formalize naming convetion
---
Code uses non-standard function names for getting data from them. Might as well reuse the Range names from D, like: popFront, front and so on. See [Source] and [TokenStream].

[Source]: https://github.com/VoltLang/Volta/blob/master/src/volt/token/source.d
[TokenStream]: https://github.com/VoltLang/Volta/blob/master/src/volt/token/stream.d

Remove Exceptions
---
Make it possible to use both the compier and Watt without Exceptions.

Avoid pointers
---
In the quest to make as much code as possible @safe we should remove usage of pointers in both [Volta] and [Watt].

[Volta]: https://github.com/VoltLang/Volta/blob/master/src/volt/token/source.d
[Watt]: https://github.com/VoltLang/Volta/blob/master/src/volt/token/source.d

Adding get and set AA's
---
In light of the two above paragrahps. Lets take a look at AAs. Both [] array index and in operator goes against these goals. Instead use a get function.

```
bool get(T[Y])(Y key, ref T)
```

Like so. Set works just like the normal array index but is there to make a set.

```
aa[key] = 4;
aa.set(key, 4); // is the same
```

