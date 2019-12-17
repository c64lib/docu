---
layout: page
title: Installation guide
shortTitle: Install
description: How to download, install and use the `c64lib`.
nav: true
order: 2
pinned: true
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
dependency to your `gradle.build` file. Put the following lines into
your `retroProject` section of the build file:

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
    
In order to build your executable you just need to execute gradle:

    gradlew
    
or

    gradlew build
    
In result C64 executables (a `prg` files) will be created.
    
### When Gradle is not used
but you really want to start using it, you have to enable it first.
Following steps are requires as preconditions:

1. Download and install JDK 8 or higher from [Oracle][java].
1. Download and install [Gradle][gradle].

Once it is done, you have to restart your console/terminal application
and go to your project location. In your location you run Gradle to install
a wrapper:

    gradle wrapper
    
then you add `gradle.build` file using following content (or similar):

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

Of course, the set of used libraries (with `libFromGitHub` element) may
vary as well as the version of Kick Assembler.

And that's it: from now on you are able to build your project using simple
`gradlew` or `gradlew build` commands. It's not even necessary to have
Gradle installed. All you need is Java 8 or higher.

## Manual clone from GitHub
If you don't want to or cannot use [Retro Assembler plugin][ra], you can
use your git client and clone libraries manually and then just point
the location with `-libdir` parameter of the KickAss.

Lets assume your project has following directory layout on the disk:

    work
      |--libs
      +--project
           |--SomeFile.asm
           +--SomeOtherFile.asm
           
Then you go to the `libs` directory (`cd work/libs`), and then clone
as many libraries from `c64lib` as you need:

    git clone https://github.com/c64lib/common.git
    git clone https://github.com/c64lib/chipset.git
    git clone https://github.com/c64lib/text.git
    git clone https://github.com/c64lib/copper64.git
    
This will checkout latest released version of the library (actually a 
top of the `master` branch, which usually means the same). In result
you will get something like this:

    work
      |--libs
      |    +--common
      |         +--lib
      |              |--common.asm
      |              |--invoke.asm
      |              |--invoke-global.asm
      |              |--math.asm
      |              |--math-global.asm
      |              |--mem.asm
      |              +--mem-global.asm
      |    +--chipset
      |         |--...
      |    +--text
      |         |--...
      |    +--copper64
      |         |--...
      +--project
           |--SomeFile.asm
           +--SomeOtherFile.asm

If you then specify `-libdir` parameter to the KickAss appropriately,
you'll be able to use the libs (asm files in `lib` directory) with
simple `#import` directive, i.e.:

    #import "common/lib/math-global.asm"
    
As mentioned earlier, checkout from `master` branch ensures that last
released version of library is used. If you want to change it and use
concrete version from the past, after `git clone` you have to enter
cloned directory (i.e. `cd common`) and checkout desired version:

    git checkout 1.0.0
    
(for version `1.0.0`).

Assembling is then possible with manual invocation of Kick Assembler:

    java -jar c:\ka\KickAss.jar -libdir ../libs SomeFile.asm
    java -jar c:\ka\KickAss.jar -libdir ../libs SomeOtherFile.asm
    

## Manual copy
Least desired method of installation of `c64lib` is to download source
code of given version and unzipping it into target directory. It is
not a very convenient method but it does not require [Gradle][gradle]
nor Git to be installed on your computer.

For every library module you have to visit GitHub and open Releases
tab:

    https://github.com/c64lib/common/releases/tag/0.1.0
    
Under assets you will see zipped content of the library. Download it
and unzip into desired location, i.e. into `libs` directory. In result
you end up with similar layout as with "Git clone" method (see above).

You use exactly the same method to use library in your source code, i.e.:

    #import "common/lib/invoke_global.asm"
    
and you invoke Kick Assembler using the same syntax:

    java -jar c:\ka\KickAss.jar -libdir ../libs SomeFile.asm

assuming, that your `libs` directory exists on the same level as your
project directory.

[gradle]: https://gradle.org/
[kickass]: http://theweb.dk/KickAssembler/Main.html
[ra]: gradle-plugin
[c64lib]: https://github.com/c64lib
[libraries]: libraries
[java]: https://www.java.com/en/download/
