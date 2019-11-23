---
layout: page
title: Installation guide
shortTitle: Install
-nav: true
order: 2
-pinned: true
---
The `c64lib` is a set of libraries that can be used in assembly programs
written with [Kick Assembler][kickass]. Each library consists of one or 
more source files that shall be then imported in Kick Assembler program.
There are several methods of downloading and "installation" of `c64lib` 
libraries, some of them will be presented there starting from the most 
convenient one.

## Using Gradle as a build tool
The easiest way to use `c64lib` is to add the libraries as dependencies
to the [Gradle][gradle] build. It is an easy task due to 
[Retro Assembler plugin][ra].

### When Gradle is already used
Well, when you are a happy user of Retro Assembler plugin, then you are
even happier, because it's extremely easy to add `c64lib` to your project.
The `c64lib` is a [GitHub][c64lib] project, so it can be added as GitHub
dependency to your `gradle.build` file:

    libFromGitHub "c64lib/common", "0.1.0"
    libFromGitHub "c64lib/chipset", "0.1.0"
    libFromGitHub "c64lib/text", "0.1.0"
    libFromGitHub "c64lib/copper64", "0.1.0"

Then you will be able to use all four libraries from `c64lib`. Of course
not all four are mandatory. You can use any subset of them as long as you
obey dependency graph as shown in [here][libraries].

Retro Assembler plugin downloads all dependencies into default location:

    .ga/deps
    
All libraries from `c64lib` will be downloaded under `c64lib` subfolder
therefore the following location:

    .ga/deps/c64lib
    
must be added to the lib dir so that Kick Assembler will see them when
interpreting `#import` directive. The following must be also a part of
your `gradle.build` file:

    libDirs = [".ra/deps/c64lib"]
    
The complete `gradle.build` will be following:

    plugins {
        id "com.github.c64lib.retro-assembler" version "1.0.1"
    }
    
    repositories {
        jcenter()
    }
    
    apply plugin: "com.github.c64lib.retro-assembler"
    
    retroProject {
        dialect = "KickAssembler"
        dialectVersion = "5.9"
        libDirs = [".ra/deps/c64lib"]
    
        libFromGitHub "c64lib/common", "0.1.0"
        libFromGitHub "c64lib/chipset", "0.1.0"
        libFromGitHub "c64lib/text", "0.1.0"
        libFromGitHub "c64lib/copper64", "0.1.0"
    }
    
### When Gradle is not used
but you really want to start using it, you have to enable it first.

### Manual clone from GitHub
If you don't want to or cannot use [Retro Assembler plugin][ra], you can
use your git client and clone libraries manually and then just point
the location with `-libdir` parameter of the KickAss.

[gradle]: https://gradle.org/
[kickass]: http://theweb.dk/KickAssembler/Main.html
[ra]: gradle-plugin
[c64lib]: https://github.com/c64lib
[libraries]: libraries
