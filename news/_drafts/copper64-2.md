---
title: Copper64 - the library, part 2
description: Here I explain, how you can make your raster IRQ handling code simpler and cleaner.
layout: post
post-url: copper64-2
categories: cbm asm c64lib
--- 
## Prepare yourself - environment setup
Before we start, get familiar with my previous [post][1], because all code I'm presenting here is available on [github](https://github.com/c64lib) in form of KickAssembler libraries. You need also [KickAssembler](http://www.theweb.dk/KickAssembler/Main.html#frontpage), a [GIT client](https://git-scm.com/downloads) and some IDE (not required), such as [Relaunch64](http://www.popelganda.de/relaunch64.html).

Because I like organizing code into libraries, there are few to clone ;-) You need to create empty directory (let's name it `c64lib`) and then clone all necessary libraries into it:
```
mkdir c64lib
cd c64lib
git clone https://github.com/c64lib/common.git
git clone https://github.com/c64lib/chipset.git
git clone https://github.com/c64lib/text.git
git clone https://github.com/c64lib/copper64.git
```

These git clones will automatically checkout `master` branch, which will contain latest stable version of library.

## References
* \[1\] [assembler-libraries][1]
* \[2\] [http://codebase64.org/doku.php?id=base:handling_irqs_with_some_simple_macros][2]
* \[3\] [http://codebase64.org/doku.php?id=base:stable_raster_routine][3]

[1]: assembler-libraries
[2]: http://codebase64.org/doku.php?id=base:handling_irqs_with_some_simple_macros
[3]: http://codebase64.org/doku.php?id=base:stable_raster_routine