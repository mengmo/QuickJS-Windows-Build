# QuickJS Windows Build

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/releases)

[![GitHub issues](https://img.shields.io/github/issues/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/issues)
[![GitHub stars](https://img.shields.io/github/stars/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/stargazers)
[![GitHub All Releases](https://img.shields.io/github/downloads/mengmo/QuickJS-Windows-Build/total?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/releases)
[![GitHub license](https://img.shields.io/github/license/mengmo/QuickJS-Windows-Build?logo=open-source-initiative)](https://github.com/mengmo/QuickJS-Windows-Build/blob/master/LICENSE)

Build [QuickJS](https://bellard.org/quickjs/) on Windows, and [prebuilt binary releases](https://github.com/mengmo/QuickJS-Windows-Build/releases).

## Build Prerequisites
Install [MSYS2](http://www.msys2.org/)

If build 64-bit QuickJS with `MINGW64`, install `x86_64-toolchain`
```
pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-make
echo "#! /bin/sh" > /mingw64/bin/make
echo "\"mingw32-make\" \"\$@\"" >> /mingw64/bin/make
```

If build 32-bit QuickJS with `MINGW32`, install `i686-toolchain`
```
pacman -S mingw-w64-i686-gcc mingw-w64-i686-make
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
make LDEXPORT="-static -s" LDEXTRAS="-static -s"
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

**[jsvu](https://github.com/GoogleChromeLabs/jsvu):** install recent versions of various JavaScript engines without having to compile them from source.
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

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Tips:** (I think there is no need to do this any more)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;When trying to load `libquickjs.dll` outside `MSYS2`/`MINGW64`/`MINGW32`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if encountered `OSError: [Error 126] The specified module could not be found.`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;try to find dlls that `libquickjs.dll` depends on with
```
  objdump -p libquickjs.dll | grep -E .dll
  objdump -p libquickjs.dll | findstr /c:.dll
```
