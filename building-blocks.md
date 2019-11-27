---
layout: page
title: Building blocks
description: The major building blocks of the `c64lib`.
pinned: false
---
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
Function is an element of Kick Assembler that is declared using
`.function` keyword. Functions does not assemble into any machine code -
they are used by assembler to evaluate values that can be then used 
in other functions, macros or to assembly a code.

## Macro

## Subroutine

## Macro-hosted subroutine

## Global importables

[chipset]: https://github.com/c64lib/chipset
