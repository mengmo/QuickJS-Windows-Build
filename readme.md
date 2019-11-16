# QuickJS Windows Build
Build [QuickJS](https://bellard.org/quickjs/) on Windows, and [binary releases](https://github.com/mengmo/QuickJS-Windows-Build/releases).

## Build Prerequisites
Install [MSYS2](http://www.msys2.org/)

Since QuickJS only support 32-bit compiling on Windows at present, we should install `i686-toolchain`
```
pacman -S mingw-w64-i686-toolchain
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
mkdir ./bin
mv qjs.exe qjsbn.exe qjsc.exe qjsbnc.exe ./bin
mkdir -p ./lib/quickjs
mv libquickjs.a libquickjs.bn.a libquickjs.lto.a libquickjs.bn.lto.a ./lib/quickjs
mkdir -p ./include/quickjs
cp -p quickjs.h quickjs-libc.h ./include/quickjs
zip -9 -r quickjs-$(cat version)-win32.zip ./bin ./doc ./examples ./include ./lib Changelog readme.txt TODO VERSION
```

## Related Projects
**[quickjs](https://github.com/PetterS/quickjs):** Thin Python wrapper of https://bellard.org/quickjs/

**[jsvu](https://github.com/GoogleChromeLabs/jsvu):** install recent versions of various JavaScript engines without having to compile them from source.