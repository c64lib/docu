---
layout: page
title: Libraries
description: What libraries are included in the `c64lib`.
pinned: true
---
The `c64lib` project consists of multiple libraries. Each library is hosted as separate GitHub repository 
and have corresponding Travis-CI entry defined (see: [https://travis-ci.org/c64lib](https://travis-ci.org/c64lib)).

There are following core libraries:
* [common](https://github.com/c64lib/common) - the one that hosts some commonly used functions, macros and subroutines; no other `c64lib` library can work without it. This code is intended to be platform (C64) independent, it complies to typical MOS 6502 assembler syntax (except it is written in KickAss, which is C64 specific at the moment).
* [chipset](https://github.com/c64lib/chipset) - the one that hosts code that is platform specific and is dedicated to handle typical C64 chipset such as VIC-II, SID, CIA as well as typical C64 memory layout.

There are following auxiliary libraries:
* [text](https://github.com/c64lib/text) - the one that handles displaying text in text mode of Commodore 64 / VIC-II.

There are following application libraries:
* [copper64](https://github.com/c64lib/copper64) - utility library that makes raster interrupt programming on VIC-II pleasant and easy.

## Library dependencies
The following diagram describes dependencies between internal libraries of `c64lib` project. 
There is also an excellent `64spec` testing library used and shown as external reference.

![Image dependencies](img/Library%20dependencies.png)

