# QuickJS Windows Build

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/releases)

[![GitHub issues](https://img.shields.io/github/issues/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/issues)
[![GitHub stars](https://img.shields.io/github/stars/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/stargazers)
[![GitHub All Releases](https://img.shields.io/github/downloads/mengmo/QuickJS-Windows-Build/total?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/releases)
[![GitHub license](https://img.shields.io/github/license/mengmo/QuickJS-Windows-Build?logo=open-source-initiative)](https://github.com/mengmo/QuickJS-Windows-Build/blob/master/LICENSE)

Build [QuickJS](https://bellard.org/quickjs/) on Windows, and [prebuilt binary releases](https://github.com/mengmo/QuickJS-Windows-Build/releases).

*Since all the modifications only affect build behaviors on Windows, you can also use this repository on Linux.*

## Build Prerequisites
Install [MSYS2](http://www.msys2.org/)

If build 64-bit QuickJS with `MINGW64`, install `x86_64-toolchain`
```
pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-make mingw-w64-x86_64-dlfcn
echo "#! /bin/sh" > /mingw64/bin/make
echo "\"mingw32-make\" \"\$@\"" >> /mingw64/bin/make
```

If build 32-bit QuickJS with `MINGW32`, install `i686-toolchain`
```
pacman -S mingw-w64-i686-gcc mingw-w64-i686-make mingw-w64-i686-dlfcn
echo "#! /bin/sh" > /mingw32/bin/make
echo "\"mingw32-make\" \"\$@\"" >> /mingw32/bin/make
```

## Obtain source code
```
git clone https://github.com/mengmo/QuickJS-Windows-Build.git
```

## Compilation
```
cd QuickJS-Windows-Build
make LDEXPORT="-static -s"
```
workaroud for **qjsc**
```
./qjsc -e -o hello.c examples/hello.js
gcc -D_GNU_SOURCE -I./ -o hello hello.c -static -s -L./ -lquickjs -lm -ldl -lpthread
./hello
```

## Packaging
```
zip -9 -r quickjs-$(cat version)-win$(echo ${MSYSTEM:0-2}).zip qjs.exe run-test262.exe
mkdir ./bin
mv qjs.exe qjsc.exe run-test262.exe ./bin
mkdir -p ./lib/quickjs
strip -g libquickjs.a
mv libquickjs.a libquickjs.lto.a ./lib/quickjs
mkdir -p ./include/quickjs
cp -p quickjs.h quickjs-libc.h ./include/quickjs
zip -9 -r quickjs-$(cat version)-win$(echo ${MSYSTEM:0-2})-all.zip ./bin ./doc ./examples ./include ./lib Changelog readme.txt TODO VERSION
```

## Related Projects
**[quickjs](https://github.com/PetterS/quickjs):** Thin Python wrapper of https://bellard.org/quickjs/

**[QuickJSPP](https://github.com/ftk/quickjspp):** QuickJS wrapper for C++.

**[QuickJS-Pascal](https://github.com/Coldzer0/QuickJS-Pascal):** Quickjs FreePascal / Delphi Bindings

**[quickjs](https://github.com/lithdew/quickjs):** Go bindings to QuickJS: a fast, small, and embeddable ES2020 JavaScript interpreter.

**[quickjs-rs](https://github.com/theduke/quickjs-rs):** A Rust wrapper for QuickJS.

**[QuickJS.NET](https://github.com/vmas/QuickJS.NET):** C# bindings for QuickJS

**[jsvu](https://github.com/GoogleChromeLabs/jsvu):** install recent versions of various JavaScript engines without having to compile them from source.

**[esvu](https://github.com/devsnek/esvu):** your one-stop shop for all implementations of ECMAScript.

#### Some efforts to make QuickJS compatible with MSVC

**[QuickJS Javascript Engine](https://github.com/c-smile/quickjspp):** QuickJS Javascript engine, MS Visual Studio port.

**[AcidJS](https://github.com/LemonHX/AcidJS):** a fork of QuickJS that compatible with MSVC and using CMAKE to compile.
* * *
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**A method to generate `libquickjs.dll`, use at your own risk.**

* Generating `libquickjs.dll` with `libquickjs.a`
```
  gcc -shared -o libquickjs.dll -static -s -Wl,--whole-archive libquickjs.a -lm -Wl,--no-whole-archive
```

* Generating `libquickjs.dll` with `libquickjs.lto.a`
```
  gcc -shared -o libquickjs.dll -static -s -Wl,--whole-archive libquickjs.lto.a -lm -Wl,--no-whole-archive
```

* Loading `libquickjs.dll` with [`ctypes`](https://docs.python.org/3/library/ctypes.html) from Python
```
  python
  from ctypes import *
  print(windll.libquickjs)
  exit()
```

* A method to get a list of QuickJS Javascript Engine API
```
  objdump -p libquickjs.dll > libquickjs_api_list.txt
```
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`QuickJS Javascript Engine API list` located in `[Ordinal/Name Pointer] Table`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Check `quickjs.h` to see what these APIs were defined for

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Tips:** (I think there is no need to do following any more)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;When trying to load `libquickjs.dll` outside `MSYS2`/`MINGW64`/`MINGW32`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if encountered `OSError: [Error 126] The specified module could not be found.`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;try to find dlls that `libquickjs.dll` depends on with
```
  objdump -p libquickjs.dll | grep -E .dll
  objdump -p libquickjs.dll | findstr /c:.dll
```

* An alternative way to `make`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Launch Command Prompt as Administrator**
```
  cd /d …\msys64\mingw64\bin
  mklink make.exe mingw32-make.exe

  cd /d …\msys64\mingw32\bin
  mklink make.exe mingw32-make.exe
```
