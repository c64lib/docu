---
title: Gradle Retro Assembler plugin 1.0.0 is out
description: Gradle Retro Assembler plugin 1.0.0 is out
layout: post
---
I would like to announce that Retro Assembler plugin for Gradle build tool is
officially released. Version 1.0.0 provides following features:
* Support for KickAssembler v4.19+
* Can assemble any number of `asm` files with KickAss with single launch of Gradle tool
* Automatically downloads proper version of KickAss and uses it
* Automatically downloads any other assembly libraries as long as they are published on GitHub
* Assembles and executes unit tests written with [64spec]

Source code and more complete documentation are available 
on [GitHub][Gradle Retro Assembler plugin]. Plugin itself is published 
on [plugins.gradle.org][plugins].

[Gradle Retro Assembler plugin]: https://github.com/c64lib/gradle-retro-assembler-plugin
[64spec]: https://64bites.com/64spec/
[plugins]: https://plugins.gradle.org/plugin/com.github.c64lib.retro-assembler/1.0.0
