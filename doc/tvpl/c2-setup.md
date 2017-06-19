---
title: Chapter 2 - Getting Set Up
layout: page
---
# Chapter 2 - Getting Set Up

The first thing to do with any programming environment is to install it and verify that it is working. So let's do that now.

## Installation

### Supported Platforms

At the time of writing *Volta*, Volt's compiler, has support for the following platforms:

* Linux (any architecture supported by [LLVM](http://llvm.org))
* Mac OS X (10.9 and higher)
* Microsoft Windows (7 and higher)

The source code is all available, so the absence of a platform is certainly no indication that it *does not* or *will not* work, only that Volta is not supported or tested there by the core maintainers. Pull requests welcome.

### Dependencies

*Volta* depends on [dmd](dlang.org), [llvm](llvm.org), and the [NASM](nasm.us). We'll also want [git](git-scm.com) to fetch the code. The next sections will detail how to install these for each platform.

#### Linux Dependency Installation

If you are on **Ubuntu** or a derivative,

	sudo apt-get install dmd llvm nasm git

will install what you need. If you're on *Arch Linux*,

	sudo pacman -S dmd llvm nasm git

will do it. Otherwise consult your OS documentation on how to install these packages.

#### OS X Dependency Installation

The easiest way of installing DMD on OS X is using [Homebrew](brew.sh). With brew installed,

	brew install dmd nasm

If you prefer not to use Homebrew, then download DMD from [here](http://dlang.org/download.html), then just extract the contents of the dmd zip and set the `DMD` environmental variable to be `/osx/bin/dmd` or put the folder `/osx/bin` on the `$PATH`.

Volt needs LLVM version 3.9, `brew install llvm` should be up to date enough.

If it isn't for some reason, you can try `brew install homebrew/versions/llvm39`, then add `/usr/local/Cellar/llvm39/3.9.X/lib/llvm-3.9X/bin` on your `$PATH` (replacing the `X`s with the real version). The reason for doing so is, that Homebrew doesn’t properly link non-core-only versions - like LLVM v3.9? if it comes from `homebrew/versions/llvm39`. For example, `llvm-config` won’t be present, but only `llvm-config-3.9`.

If you're not using Homebrew, download LLVM from the [LLVM homepage](llvm.org), and put the `bin` folder inside the unpacked tarball on the `$PATH`.

[You'll also need to install git.](https://git-scm.com/download/mac)

#### Windows Dependency Installation

Install [DMD.](http://dlang.org/download.html).

Install [Microsoft Visual Studio Community](https://www.visualstudio.com), for free. Microsoft has a habit of changing URLs monthly, so you'll have to click around for the download page. We believe in you!

Install [Cmake.](https://cmake.org/) and use it to compile the [LLVM](llvm.org) source code. [LLVM have more detailed documentation on how to do this.](http://llvm.org/docs/CMake.html) Be sure to compile in 'Release' mode; 'Debug' LLVM builds have a tendency to crash on Windows.

Install [NASM.](http://www.nasm.us/). Ensure that it is available on your `%PATH%`.

Finally, [download and install git.](https://git-scm.com/download/win) Make sure it's on your `%PATH%`.

### Getting Battery

Download [Battery](https://github.com/VoltLang/Battery/releases), Volt's build tool. Make sure it's on your `PATH`.

### Getting The code

Now that the dependencies have been installed, let's set up a place to build *Volta* and the tools it needs. For simplicity, this tutorial will assume this location is directly in your `$HOME` directory/User folder, but you can place it where you want.

	mkdir volt
	cd volt

Next, let's get the code for *Volta* (the compiler), and *Watt* (the standard library).

	git clone git@github.com:VoltLang/Volta.git
	git clone git@github.com:VoltLang/Watt.git

## Hello World!

### The Program

Create a new folder, `hello`. In this folder would go all the code and tests that make up the project. All we want to do is print a message to the screen.

Save this code into a file called `hello.volt`, and put that in a `src` folder inside the `hello` folder.

	import watt.io;

	fn main() i32
	{
		writeln("hello, world");
		return 0;
	}

Then save the following in the root of the folder in a file called `battery.txt`.

	--dep
	watt

For more information on Battery, check out [its documentation](https://github.com/VoltLang/Battery/blob/master/doc/index.md).

### Compiling and Running

First, configure the build.

	battery config /path/to/Volta /path/to/Watt .

Then, to build the `hello` executable,

	battery build

If an error is produced, make sure your code matches what's written above exactly. Otherwise, run `./hello` (on Linux and OS X) or `hello.exe` (on Windows) to run your new program! The message `hello, world` should be printed onto the screen. If it worked, everything seems to be setup correctly. [On to the next chapter!](c3-steps.html) If you're still confused, keep reading.

## Finding Help

In programming, as in life, things often don't work how you'd expect. Before you ask for help make sure you've tried to solve your problem on your own; problem solving is an important part of writing programs. But if you're truly stumped, the best place to ask for help is on the `#volt` IRC chat channel, hosted on [Freenode](https://webchat.freenode.net/). You can connect with a desktop client, or follow that link to use Freenode's own web client.
