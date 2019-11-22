# QuickJS Windows Build

[![GitHub release (latest by date)](https://img.shields.io/github/v/release/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/releases)

[![GitHub issues](https://img.shields.io/github/issues/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/issues)
[![GitHub stars](https://img.shields.io/github/stars/mengmo/QuickJS-Windows-Build?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/stargazers)
[![GitHub All Releases](https://img.shields.io/github/downloads/mengmo/QuickJS-Windows-Build/total?logo=github)](https://github.com/mengmo/QuickJS-Windows-Build/releases)
[![GitHub license](https://img.shields.io/github/license/mengmo/QuickJS-Windows-Build?logo=open-source-initiative)](https://github.com/mengmo/QuickJS-Windows-Build/blob/master/LICENSE)

Build [QuickJS](https://bellard.org/quickjs/) on Windows, and [binary releases](https://github.com/mengmo/QuickJS-Windows-Build/releases).

## Build Prerequisites
Install [MSYS2](http://www.msys2.org/)

If build 64-bit QuickJS with `MINGW64`, install `x86_64-toolchain`
```
pacman -S mingw-w64-x86_64-toolchain
echo "#! /bin/sh" > /mingw64/bin/make
echo "\"mingw32-make\" \"\$@\"" >> /mingw64/bin/make
```

If build 32-bit QuickJS with `MINGW32`, install `i686-toolchain`
```
pacman -S mingw-w64-i686-toolchain
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
make LDFLAGS="-static -s"
```

## Packaging
```
zip -9 -r quickjs-$(cat version)-win$(echo ${MSYSTEM:0-2}).zip qjs.exe qjsbn.exe run-test262.exe run-test262-bn.exe
mkdir ./bin
mv qjs.exe qjsbn.exe qjsc.exe qjsbnc.exe run-test262.exe run-test262-bn.exe ./bin
mkdir -p ./lib/quickjs
mv libquickjs.a libquickjs.bn.a libquickjs.lto.a libquickjs.bn.lto.a ./lib/quickjs
mkdir -p ./include/quickjs
cp -p quickjs.h quickjs-libc.h ./include/quickjs
zip -9 -r quickjs-$(cat version)-win$(echo ${MSYSTEM:0-2})-all.zip ./bin ./doc ./examples ./include ./lib Changelog readme.txt TODO VERSION
```

## Related Projects
**[quickjs](https://github.com/PetterS/quickjs):** Thin Python wrapper of https://bellard.org/quickjs/

**[jsvu](https://github.com/GoogleChromeLabs/jsvu):** install recent versions of various JavaScript engines without having to compile them from source.
