---
layout: drawpile_help
title: Coding Guideline
description: "Drawpile code contribution guidelines."
date: 2024-03-30 00:00:00 +0200
category: help
tag: help development
---

This page contains a general guideline for development contributions to Drawpile.

Read [the architecture page](architecture) to become familiar with the different components, and check out [the contributing page](contributing) for general guidelines.

* Table of contents
{:toc}

## Organizing

Get in contact! Be it [on IRC](https://drawpile.net/irc/){:target="_blank"}, [on Discord](https://drawpile.net/discord/){:target="_blank"} or [in the GitHub Issues](https://github.com/drawpile/Drawpile/issues){:target="_blank"}.

If you'd like to take on a task, we strongly recommend getting in contact with us and the other developers in our community, to avoid conflicts or potentially working on something that is already being worked on. This is especially important when it comes to the network protocol or similar pillar components because of the potential for merge conflicts, which can be avoided if we know that you're working on something.

## Code Style

Auto-format code.

Use clang-format for C C++, rustfmt for Rust, gofmt for Go, Prettier for JavaScript and TypeScript, black for Python.

Some files aren't auto-formatted yet. When you make changes to them, either leave them be and make your new code follow the ambient format or auto-format them in a separate commit. If it's a really huge file, you can also put `// clang-format off` at the beginning of the file and use `// clang-format on` to only format certain sections to let the file become more formatted over time.

Insert a `SPDX-License-Identifier` comment at the beginning of your files. For the parts that still use large verbose comment block, either leave them be or replace them with an SPDX comment.

## Attributions

If you take code from elsewhere, attribute it properly, ideally with an `SPDX-Snippet-` block like so:

```c
// SPDX-SnippetBegin
// SPDX-License-Identifier: Some-License-Identifier
// SDPX—SnippetName: implementation for a taken from project b

...code goes here...

// SPDX-SnippetEnd
```

Don't use proprietary code from elsewhere, or any code with an incompatible license.

Avoid GitHub Copilot, ChatGPT or any other machine-learning-based tool that will regurgitate someone else's improperly-licensed code.

## Dependencies

Keep dependencies to an absolute minimum; they are a maintenance burden and make building Drawpile harder, especially in the long run.

Remember that we have to get every dependency to build on a many different platforms: Linux (Flatpak and AppImage), macOS (Intel and Apple Silicon), Windows (x86 and x64), Android (arm32, arm64, x86 and x64; APK and F-Droid) and WebAssembly.

To make building from source as easy as possible, dependencies must either be available in common Linux distributions and vcpkg or be bundled with Drawpile.

Test and keep things compatible with both Qt5 and Qt6. Qt6 has stopped supporting Windows 7 and old Android devices, which we are not willing to cut out. Qt6 also doesn't come with ANGLE anymore, which is required for the OpenGL hardware-accelerated canvas renderer.

## Avoid Troublesome Features

The C++ language and the Qt framework have some parts that should either be avoided or used sparingly.

* Avoid exceptions and longjmp. The performance is awful, especially in the browser.

* Avoid overuse of RAII. Since Drawpile doesn't use exceptions, you don't have to worry about constructors bailing out early, so it's not necessary to put an RAII wrapper around everything.

* Avoid templates. They stretch compile times into eternity and produce utterly incomprehensible error messages.

* Avoid function name overloading. The error messages about type mismatches are long enough if there's only one candidate. This includes `const` overloading. Just give different functions different names.

* Avoid implicit conversion constructors. They mask errors. Put `explicit` on constructors that can take one argument.

* Avoid `auto`. It masks errors if C++ goes on an implicit conversion spree. If the type is particularly egregious to type and isn't otherwise interesting (e.g. an iterator), then it's fine to use it.

* Avoid overusing `const`. For local variables, it's often just noise. For classes, don't use it to the point where you have to write every member function twice and have immense amounts of logic in the syntactic quagmire of the constructor initialization list.

* Avoid ranges from C++20. They take an eternity to compile.

* Avoid std::array if possible. It's verbose and produces terrible error messages, just use a C-style array instead.

* Avoid placement new. MSVC will not return the same address you put into it, so it's effectively unusable.

* Avoid using Qt enums in settings directly, use their integer values instead. The enums are fragile, you can't move or rename them without them breaking. They also break when switching between Qt5 and Qt6.

* Avoid Qt interfaces that start a nested event loop, such as `QDialog::exec` or the static functions in `QMessageBox` and `QInputDialog`. Basically anything that opens a dialog and then returns the result when the user closes it, suspending the current function. This kind of thing is not properly supported under WebAssembly.

* Avoid dealing with settings or Qt metatype attributes during static initialization. They wouldn't be loaded properly yet and will cause assertion failures when using Qt in debug mode.

* Avoid Qt's synchronization primitives (QMutex friends) in places where performance matters. On Windows, they are extremely slow. Use DP\_Mutex and friends instead, they wrap the platform's synchronization primitives instead and are much faster.

* Using Qt Designer for UIs is mildly discouraged. It's pretty clunky, and often makes a mess of stuff like the tab order, making it harder to figure out where connections are made. The merge conflicts it generates are also virtually unsolvable, usually leading to having to do the work twice. However, if it makes your task much easier, feel free to use it.

* In general, if you have the choice between a "fancy", "clean" C++ or Qt metaprogramming way to solve something and a simple, dirty preprocessor macro or similar, pick the latter. The chances that MSVC will misbehave or that you'll have bad behavior in some Qt version are pretty high. Experience says that these will be crashing bugs too, which aren't worth the little extra prettiness.

## Comments

Don't write Doxygen/JavaDoc style comments, their structure is too repetitive. If you want to document something in a comment, just write it in plain prose instead.

## You Ain't Gonna Need It

Do not design with imaginary future extensibility. Usually this just leads to more complex code that in the end isn't extensible where it's needed. Simpler code is more malleable and can be changed more easily when new requirements come up.

## Contribution Notes

Some parts of the code that flagrantly violate the guidelines above. They continue to cause problems and will probably need to be rewritten at some point.

Please don't submit pull requests that only change formatting and nothing else. You can adjust the code that you're actually working on, but making sweeping changes usually causes issues.
