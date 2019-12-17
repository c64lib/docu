---
layout: page
title: Building blocks
description: The major building blocks of the `c64lib`.
pinned: true
---
There are plenty of elements that can be declared within library and then
reused. In most cases a dedicated Kick Assembler syntactic elements are used.
These elements are declared source files (`asm`) which can be used by your
programs by using `#import` directive. 

Most of the library source files have `#importonce` flag set - that is given
library file can be imported in many places (in direct or indirect way) and
only first import is effective. That also means that libraries can import
themselves (and in fact they do!).

This document describes all kinds of elements that are declared by `c64lib`.

## Namespace
All elements of `c64lib` are defined within `c64lib` namespace, that is,
they are visible only inside this namespace. This approach prevents possible
name clashes between possible other libraries that could be used together
with `c64lib` in a project.

## Label
A label is a value tagged with name. Labels are used to give symbolic names
to values denoting addresses, registers or constants.
Labels are useful to define memory locations and are widely used in [chipset] 
library:

    .label VIC2                 = $D000 
    .label SPRITE_0_X           = VIC2 + $00 
    .label SPRITE_0_Y           = VIC2 + $01 
    .label SPRITE_1_X           = VIC2 + $02 
    .label SPRITE_1_Y           = VIC2 + $03 
    .label SPRITE_2_X           = VIC2 + $04 
    .label SPRITE_2_Y           = VIC2 + $05 

Labels are always declared inside `c64lib` namespace.
Labels can be reached outside `c64lib` namespace by prefixing their names with
namespace name (labels are the only elements that can be accessed that way due
to Kick Assembler limitations). For example: at any time it is legal to write:

    lda #100
    sta c64lib.SPRITE_0_X

## Function
A function is an element of Kick Assembler that is declared using
`.function` keyword. Functions does not assemble into any machine code -
they are used by assembler to evaluate values that can be then used 
in other functions, macros or to assembly a code.

Example of function that negates (inverts all bits) of its argument:

    .function neg(value) {
      .return value ^ $FF
    }

Example of function that calculates packed value of memory register based 
on its arguments:

    /*
     * Params:
     * video: location of video ram: 0..15
     * bitmap: location of bitmap definition: 0..1
     */
    .function getBitmapMemory(video, bitmap) {
      .return bitmap<<3 | video<<4
    }

Functions are always declared inside `c64lib` namespace. Functions are
declared in files `.asm` files located under `lib` directory.

## Macro
A macro is an element of Kick Assembler that is declared using `.macro`
directive. Once used, macro is replaced with assembly code defining it.
Macros can be parametrised (that is, they can take an argument, or even
more arguments) - this parametrisation can affect the code being
generated.

It is noteworthy that macro is not an equivalent of subroutine, 
even though it looks similar to one from syntactic point of view. 
When macro is "called":

    someFancyMacro(parameter1, parameter2)
    
it does not mean, that your code will jump into place where macro 
`someFancyMacro` is declared, pass both parameters and return from that 
place once execution is finished. Instead, an assembler will paste
code declared inside macro substituting parameters with values provided
as `parameter1` and `parameter2`.

Under some circumstances you can use macros as subroutines. This may be fast, 
because there is no subroutine calling overhead. This will however consume a
lot of memory (in sense of generated machine code) if not used wisely.

Macros are declared in `.asm` files located under `lib` directory.

## Subroutine
As subroutine we understand a piece of ML code that can be used by jumping
there with `jsr` operation. A subroutine always ends with `rts` which means
that at the end of execution program counter will be restored to the position
right after original `jsr` operation and code execution will continue. In
this sense a subroutine is an equivalent of procedure, function or method in
high level programming languages.

Kick Assembler as such does not provide any special means to create
subroutines as it is just a macro assembler. With `c64lib` we basically share
subroutine code just by writing piece of asm code and place it in separate
source files.

Subroutines are declared in `.asm` files located under `lib/dir` subdirectory.
Each subroutine consists of appropriate `rts` operation so that it should always
be accessed with corresponding `jsr` operation. If soubroutine consumes input
parameters, they should be set accordingly before `jsr` is executed.
Depending on the parameter passing method it should be either register setup
(that is A, X or Y), memory location setup or pushing to the stack. For stack
method there is a convenience library [invoke] available.

A subroutine code should be imported in place where it needs to be 
located - we don't do it at the top of the source file but rather we use 
`#import` directive exactly in place where we want to have our subroutine.

Lets consider [copyLargeMemForward] subroutine as an example. We have to label
a place in memory where the subroutine will start and then import the
subroutine itself:

    copyMemFwd:
        #import "common/lib/sub/copyLargeMemForward.asm"
        
The subroutine takes three parameters using stack passing method:

    Stack WORD - source address
    Stack WORD - target address
    Stack WORD - size

So, before calling subroutine, you have to push 6 bytes to the stack. The
easiest way to do it is to use [invoke] library:

    #import "common/lib/invoke-global.asm"
    
and then:

    c64lib_pushParamW(sourceAddress)
    c64lib_pushParamW(destinationAddress)
    c64lib_pushParamW(amountOfBytesToCopy)
    jsr copyMemFwd

In result a subroutine will be called and `amountOfBytesToCopy` bytes will be
copied from `sourceAddress` location to the `destinationAddress` location.

## Macro-hosted subroutine
Some subroutines use this convenient method of distribution. Instead of being 
declared in separate source file, they are declared where macros and functions
are declared - in library source files itself.

Macro-hosted subroutines are used when further parametrisation is needed before
subroutine is ready to use. Usually there are some variants that can be turned
on or off (in such case such macro can be called multiple times thus generating
multiple versions of subroutine). Sometimes subroutine requires some zero-page
addresses that we don't want to hardcode in the library - it would be then
up to the user to parametrise subroutine with addresses of choice.

Example - a scroll subroutine

Let's consider [scroll1x1]:

This subroutine requires three parameters being passed via stack but also needs
two consecutive bytes on zero page for functioning (indirect addressing is
used). Let's assume we will use address 4 and 5 for this purpose.

    #import "text/lib/scroll1x1.asm"
    #import "common/lib/invoke.asm"
    
    ...
    
    .namespace c64lib {
        pushParamW(screenAddress)
        pushParamW(textAddress)
        pushParamWInd(scrollPtr)
    }
    jsr scroll
    
    ...
    
    scroll: .namespace c64lib { scroll1x1(4) }
    
So, the scroll subroutine is configured for address 4 (and 5), and installed
under address denoted by `scroll` label. It can be then normally called with
`jsr scroll`. Before calling input parameters need to be pushed to the stack.
It is done via `pushParamW` macros (for address values) and `pushParamWInd` (to
extract value from memory location pointed by parameter).

## Global importables
Macros and functions from `c64lib` are declared within `c64lib` namespace. As
already mentioned this is to avoid name clashes when using libraries from
different sources. Due to KickAssembler limitations it is not possible to
access macro or function from within namespace using "dot" notation. In order
to allow accessing elements from outside `c64lib` namespace, each library file
has `_global` counterpart that can be alternatively imported. In such case
all elements such as functions and macros will be available straight from
root namespace by using `c64lib_` name prefix.

Example - you can use elements from [invoke] in traditional way:

    #import "common/lib/invoke.asm"
    
    .namespace c64lib {
        pushParamW(sourceAddress)
        pushParamW(destinationAddress)
        pushParamW(amountOfBytesToCopy)
    }
    
or by using global importable:

    #import "common/lib/invoke-global.asm"
    
    c64lib_pushParamW(sourceAddress)
    c64lib_pushParamW(destinationAddress)
    c64lib_pushParamW(amountOfBytesToCopy)

[chipset]: https://github.com/c64lib/chipset
[invoke]: https://github.com/c64lib/common/blob/master/lib/invoke.asm
[copyLargeMemForward]: https://github.com/c64lib/common/blob/master/lib/sub/copy-large-mem-forward.asm
[scroll1x1]: https://github.com/c64lib/text/blob/develop/lib/scroll1x1.asm
