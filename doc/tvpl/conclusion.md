---
title: Conclusion
layout: page
---
# Conclusion

That's been our whirlwind tour of Volt. If you're confused about anything at all, please do not hesitate to contact us. IRC, [as detailed in chapter 2](c2-setup.html) is probably the best way.

## Useful Tools

Along with the core toolchain, there are a few other libraries and tools that you might find useful.

[Tesla](https://github.com/VoltLang/Tesla) is Volt's test suite. Along with containing the language tests, *Tesla* can be used to write tests for your own programs.

[Battery](https://github.com/VoltLang/Battery) is the standard build tool for *Volt* programs. You can use Makefiles and the like, but *Battery* is written in and used by Volt, so it can be very clever.

[Fourier](https://github.com/VoltLang/Fourier) is a tool that can help generate modules automatically that will let you use C libraries from Volt. You can write these by hand, but this can be a very tedious task; Fourier may be able to save you a lot of time!

[Amp](https://github.com/VoltLang/Amp) is a collection of maintained bindings to popular libraries, like SDL and OpenGL.
