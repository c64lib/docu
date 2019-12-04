---
layout: page
title: Building blocks
description: The major building blocks of the `c64lib`.
pinned: false
---
There are plenty of elements that can be declared within library and then
reused. In most cases a dedicated Kick Assembler syntactic elements are used.
These elements are declared source files (`asm`) which can be used by your
programs by using `#import` directive. 

Most of the library source files have
`#importonce` flag set - that is given library file can be imported in many
places (in direct or indirect way) and only first import is effective. That also
means that libraries can import themselves (and in fact they do!).

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

## Macro
A macro is an element of Kick Assembler that is declared using `.macro`
directive. Once used, macro is replaced with assembly code defining it.
Macros can be parametrised (that is, they can take an argument, or even
more arguments) - this parametrisation can affect the code being
generated.



## Subroutine

## Macro-hosted subroutine

## Global importables

[chipset]: https://github.com/c64lib/chipset
