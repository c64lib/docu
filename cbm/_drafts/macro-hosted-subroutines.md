---
title: Conan the Librarian - subroutines
description: How to write library subroutines
layout: post
post-url: macro-hosted-subroutines
---
Subroutine is an essential concept in programming. Advantages of using subroutines are quite clear - instead of repetitive work we define chunk of code once and "jump" to it any time we need it. It is a part of DRY principle (do not repeat yourself), which allows to keep our code compact and maintainable. It allows us to keep memory footprint of our code at reasonable level. With possibility of passing execution arguments as well as result, subroutines become fully blown procedures and functions as known from high level languages.

How does MOS 6502 support subroutines? We essentially need two instructions: "**J**ump to **S**ub**R**outine" that does typical long jump to given memory address and "**R**e**T**urn from **S**ubroutine" that returns to the address pointing to the next instruction after "jump". These instructions are named `JSR` and `RTS`, respectively. `JSR` does actually more than simple `JMP` - it pushes return address to the stack (MOS 6502 uses memory page 1 for stack: addresses from `$100` to `$1FF`). `RTS` pulls return address from stack and stores it as program counter (PC).

For all of you my readers who like to be very accurate, the address stored by `JSR` is decremented by 1, `RTS` does increment it back by 1. I don't even want to know why, I bet somebody wanted to make some savings on transistor count, it's just my quess...

Communicating with subroutines - parameters
-------------------------------------------
In 6502 machine code there are at least three commonly used methods for passing parameters to the subroutine:
1.   Using processor registers.
2.   Using fixed memory locations (via global variables).
3.   Using stack.

Using CPU registers is the simplest possible way of communication. Assuming that registers are used wisely, we may get CPU registers already properly loaded with values which speeds up execution and can be essential in some time critical cases. There is however one downside of this method directly connected with very simplistic design of 6502: there are only 3 registers that can be easily used (`A`, `X` and `Y`) each only 1 byte in size - not much indeed.

Fixed memory location for exchanging parameters is fine unless you write libraries of code.