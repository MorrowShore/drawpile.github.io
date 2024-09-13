---
layout: drawpile_help
title:  "Building from Source"
description: "How to build Drawpile from its source code."
date: 2023-12-16 00:00:00 +0200
category: "help"
tag: help development
---

Compiling Drawpile from its source code is only necessary if you want to develop on it yourself or if you have a system for which there's no official releases provided. Those official releases can be found [on the download page](https://drawpile.net/download/).

Building stuff from source can be tricky, so if you have any issues, take a look at [the help page](https://drawpile.net/help/) on where you can ask for help!

Pick the operating system you want to build Drawpile for:

* [Linux](#linux)
* [macOS](#macos)
* [Windows](#windows)
* [Android](#android)
* [WebAssembly](#webassembly)
* [Other Operating Systems](#other-operating-systems)

## Linux

Compiling software on Linux is usually pretty easy, the setup should only take a few minutes. However, the names of the dependencies tend to be different on the various flavors of Linux.

The following instructions should work on **Ubuntu**, **Debian** and anything based on them. For other distributions, you'll have to find the matching dependencies for the stuff listed in step 1. If you need help with this (or anything else), take a look at [the help page](https://drawpile.net/help/) on how to get in touch.

1. Open a terminal and run `sudo apt install build-essential cmake git libqt6svg6 qt6-base-dev qt6-l10n-tools qt6-multimedia-dev qt6-tools-dev qt6-websockets-dev libxkbcommon-dev libzip-dev zipcmp zipmerge ziptool libmicrohttpd-dev libsodium-dev libsystemd-dev qtkeychain-qt6-dev libwebp-dev`
2. [Install Rust](https://www.rust-lang.org/tools/install){:target="_blank"}
3. Run `git clone https://github.com/drawpile/Drawpile.git` to clone Drawpile
4. Run `cd Drawpile` to switch into the directory you just cloned
5. Configure the project by running *one* of the following, depending on what you want to do:
    * `cmake --preset linux-debug-qt6-all-make` if you want develop on Drawpile
    * `cmake --preset linux-release-qt6-all-make` to build everything for use on a desktop
    * `cmake --preset linux-release-qt6-server-make` to build the server portion only, without any graphical interface
6. Run `cmake --build build` to build it
7. The resulting applications (client, server, command line tools) will be in `build/bin` and you can run them from there. You can also run `sudo cmake --install build` to install Drawpile into your system.

## macOS

Building Drawpile on macOS should be reasonably straight-forward, although you have to install a number of external tools first.

1. [Install Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12){:target="_blank"}
2. [Install Homebrew](https://brew.sh/){:target="_blank"}
3. [Install Rust](https://www.rust-lang.org/tools/install){:target="_blank"}
4. Open Terminal.app and run `brew install cmake libzip qt libmicrohttpd libsodium qtkeychain` to install the dependencies
5. Run `git clone https://github.com/drawpile/Drawpile.git` to clone Drawpile
6. Run `cmake -S Drawpile -B Drawpile-build -G Xcode` to generate the Xcode project
7. Run `open Drawpile-build/Drawpile.xcodeproj` to open the project in Xcode and use normally

## Windows

Building software on Windows is a pretty miserable experience. It usually takes **at least 3 hours** for the development environment to finish installing, possibly more depending on the speed of your computer and network. You should also have **at least 50 GB of free disk space**, otherwise the process may fail.

Don't hesitate to ask for help if you're having trouble, you can find multiple places to do so on [the help page](https://drawpile.net/help/).

1. Install Rust and Visual Studio Community Edition. Download [the Rust installer](https://static.rust-lang.org/rustup/dist/x86_64-pc-windows-msvc/rustup-init.exe){:target="_blank"} and run it. You can leave everything on the default options to let in install everything that's needed.
2. Run the Visual Studio x64 Command Line. You should be able to find it in your start menu by searching for "x64". You should see a black window with white text in it.
    1. If you haven't used the Windows command line before: it's weird and all kinds of terrible.
    2. **Do not press `Ctrl+V`**, it won't paste, it will just write garbage that you have to delete or else it will mess things up. To paste something, you have to use `Shift+Insert` instead.
    3. **Do not click around in the window.** Only click the title bar or the entry in the task bar. Else it will get stuck until you press the escape key. You know if you got into this state if it says "Select" in the title bar of the window.
    4. **Don't try to copy stuff out of the window.** That will almost always get it stuck and/or kill whatever is currently running. Just take a screenshot instead if you want to show someone what it says.
3. Install vcpkg:
    1. Run `cd /D C:\` (type it into the command line window and hit enter) to switch to the root of the C drive. (Note: don't try to be experimental and put it into a different directory. It's very fragile. If the path is too long, contains a symlink or a space anywhere, vcpkg will not work properly. Save yourself the wasted time and just put it into the root of your C drive.)
    2. Then run `git clone https://github.com/Microsoft/vcpkg.git` to get vcpkg.
    3. Then `.\vcpkg\bootstrap-vcpkg.bat -disableMetrics` to set up vcpkg.
4. Install the dependencies:
    1. This step takes really long to complete. Again, make sure you have at least 50 GB of free disk space.
    2. Run `vcpkg --disable-metrics install --clean-after-build qt5-base:x64-windows qt5-multimedia:x64-windows qt5-svg:x64-windows qt5-tools:x64-windows qt5-translations:x64-windows qt5-websockets:x64-windows kf5archive:x64-windows libmicrohttpd:x64-windows libsodium:x64-windows qtkeychain:x64-windows` to install the dependencies.
        * **Note:** as of April 2024, xz is broken in vcpkg. If you get an error about `https://github.com/tukaani-project/xz/archive/v5.4.4.tar.gz` not being available, you have to apply [this workaround](https://github.com/microsoft/vcpkg/issues/37839#issuecomment-2028011285){:target="_blank"} and run the above command again.
    3. Go do something else for the next several hours while it installs everything.
5. Build Drawpile:
    1. Run `cd /D %HOMEDRIVE%%HOMEPATH%` to switch to your home directory. It is probably under `C:\Users\<YOUR NAME>`.
    2. Then run `git clone https://github.com/drawpile/Drawpile.git` to get Drawpile's source code.
    3. Then `cd Drawpile` to switch into that directory.
    4. Configure the project by running *one* of the following, depending on what you want to do:
        * `cmake --preset windows-x64-debug-qt5-all-ninja` if you want develop on Drawpile
        * `cmake --preset windows-x64-release-qt5-all-ninja` to build everything for use on a desktop
    5. Build it: `cmake --build build` - this takes a few minutes the first time around.
    6. Install it: `cmake --install build` - this won't overwrite any existing installation of Drawpile, it will just put the stuff you built and the DLLs, icons and such together so that you can actually run it.
6. Run what you built. The executable should be at `C:\Users\<YOUR NAME>\Drawpile\install\drawpile.exe`

Sometimes cmake fails to generate the PDB paths (debug info stuff.) If that happens and you need them, delete the build directory and try again.

## Android

Unfortunately, building on Android is such a mess that there's currently no straight-forward instructions for it. Just about every update to the toolchain seems to break things in new and exciting ways.

If you want to try anyway, your best bet is to look at [the GitHub Actions workflow](https://github.com/drawpile/Drawpile/blob/main/.github/workflows/main.yml){:target="_blank"} and follow things from there. Also, come join the Discord or IRC channel listed [on the help page](https://drawpile.net/help/) so that you can get some support and a place to rant on. If you succeed, we'll document the process here!

## WebAssembly

Building on WebAssembly has only been tested on Linux. Your mileage may vary on other operating systems.

You will need to install the prerequisites to build Qt on your host system. On Debian-based systems, this also includes `ninja-build`, `libfontconfig1-dev` and `libssl-dev`.

```sh
# Get the Emscripten SDK, version 3.1.50
git clone https://github.com/emscripten-core/emsdk.git
cd emsdk
./emsdk install 3.1.50
./emsdk activate latest
cd ..

# Grab Drawpile's source code
git clone https://github.com/drawpile/Drawpile.git
cd Drawpile

# Activate Emscripten in your current shell
. ../emsdk/emsdk_env.sh

# Set up the dependencies (will take several hours)
pkg/emscripten/setup.bash debug

# Configure Drawpile
pkg/emscripten/configure.bash debug

# Build it (linking will take several minutes)
cmake --build buildemdebug

# Create a servable directory
cmake --install buildemdebug
```

You will end up with the application ready in a directory called `installemdebug`.

You can use `qtwasmserver` to serve it from there, see [Qt's documentation on that](https://doc.qt.io/qt-6/wasm.html#qtwasmserver). Running it with something like Python's http.server will *not* work because that doesn't set some required headers.

## Other Operating Systems

Drawpile should theoretically run on any platform that support the following:

* CMake, at least version 3.18, but 3.24 or newer is recommended.
* Rust, at least version 1.66.1.
* A C++17 compiler.
* Qt, either version 5 or 6. Drawpile only regularly gets built on the latest version of these, so your mileage may vary on older ones.

Refer to the [Linux](#linux) section above with regards to which dependencies you need to install. You can also look at [the GitHub Actions workflow](https://github.com/drawpile/Drawpile/blob/main/.github/workflows/main.yml){:target="_blank"} for how the official releases are built, but it's more involved.

If you succeed with build Drawpile on your operating system, it'd be cool to know how you did it! For that and if you need help along the way, take a look [at the help page](https://drawpile.net/help/).
