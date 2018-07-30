---
title: Copper64 - the library, part 1
description: Here I explain, how you can make your raster IRQ handling code simpler and cleaner.
layout: post
post-url: copper64-1
---
One of the greatest feature of VIC-II is that it can a source of IRQ interrupts. VIC can generate interrupt on sprite collision, lightgun shots and most importantly on specified raster line. 

This last feature is especially useful because it allows to do some tricks such us screen splitting (so that you can have two or more screen areas displaying different screen modes or scrolled independently). You can play with global color registers to get more colorful display, you can play with sprite registers to show more than 8 of them at the same time. Actually raster IRQ is so important in C64 world, that it is even used when playing background music.

Although having raster interrupt is definitely better than not having it (owners of VIC-20 or even PC can say something about it), but still there are platforms, that have much better and more convienient support (Amiga, but also a8i can easily mix graphic modes with display list).

Let's look at following example:

	.label IRQ_1 = 150
	.label IRQ_2 = 200

	start:
	  sei                         // disable IRQ, otherwise C64 will crash
	  lda #$7f                    // stop CIA from producing IRQ
	  sta c64lib.CIA1_IRQ_CONTROL
	  sta c64lib.CIA2_IRQ_CONTROL
	  lda c64lib.CIA1_IRQ_CONTROL
	  lda c64lib.CIA2_IRQ_CONTROL
	  lda #c64lib.IMR_RASTER      // VIC-II is about to produce raster interrupt
	  sta c64lib.IMR
	  setRaster(IRQ_1)
	  lda #<irq1
	  sta c64lib.IRQ_LO
	  lda #>irq1
	  sta c64lib.IRQ_HI
	  configureMemory(c64lib.RAM_IO_RAM)  // turn off kernal, so that our vector becomes visible
	  cli
	block:
	  jmp block                   // go into endless loop
	  
	irq1: {  
	  irqEnter()
	  inc c64lib.BG_COL_0         // change background color 
	  inc c64lib.BORDER_COL       // change border color
	  irqExit(irq2, IRQ_2, false)
	}  

	irq2: {
	  irqEnter()
	  dec c64lib.BG_COL_0         // change it back
	  dec c64lib.BORDER_COL       // change it back
	  irqExit(irq1, IRQ_1, false)
	}
	
Here I use several macros, that are defined in [chipset library](https://github.com/c64lib/chipset). I will keep them just explaining their meaning, for better code clarity:
* ```setRaster(rasterNum)``` - sets raster number into VIC-II (including 8-th bit)
* ```configureMemory``` - sets C64 memory layout (address $00)
* ```irqEnter``` / ```irqExit``` - nice raster helper macros, inspired by [1].

Once assembled and launched, this code produces following picture:

![Simple raster]({{ "/cbm/img/copper64/simple-raster-jitter.png" | absolute_url }})

One of VIC-II shortcomings is the fact, that you can specify only single raster line and single IRQ handler at the moment. That is, to fire several different raster handlers at certain screen positions you have to dynamically reprogram VIC-II at the end of one handler to register another handler. And so on.

Second shortcomming is visible on the screenshot above: we clearly see the moment when background color is changed and it's almost in the middle of the screen. Even worse: positions of these ugly "steps" are not stable, but they tend to jitter forth and back. I will explain this in details in my next article, today we will just believe that there are ways to compensate this, however not very simple. One good example is described in [2].

Both Amiga and 8-bit Atari have dedicated processor that executed special program called display list: it frees programmer with doing all ugly things by himself(herself) and, what's most important, it is *stable* (well, that's not always the case in Atari). The question is whether we can emulate such feature with Commodore 64.

## Better way - use copper64 library
I wrote this little library to make our lives easier. It is available on [github](https://github.com/c64lib/copper64) and can be freely used as it is open source. When designing it, I had two goals:
1. "Display list" should be easily definable in form of simple, tabular data structure.
1. Visual effects triggered in result of this display list should be as stable as possible.

How about getting such image:

![Colorful raster bars]({{ "/cbm/img/copper64/color-raster-bars.png" | absolute_url }})


with following code?:

	.align $100
	copperList: {
	  copperEntry(46, c64lib.IRQH_BORDER_COL, WHITE, 0)
	  copperEntry(81, c64lib.IRQH_BG_COL_0, YELLOW, 0)
	  copperEntry(101, c64lib.IRQH_BG_COL_0, LIGHT_GREEN, 0)
	  copperEntry(124, c64lib.IRQH_BG_COL_0, GREY, 0)
	  copperEntry(131, c64lib.IRQH_BG_COL_0, BLUE, 0)
	  copperEntry(150, c64lib.IRQH_BORDER_COL, RED, 0)
	  copperEntry(216, c64lib.IRQH_BORDER_BG_0_COL, LIGHT_GREY, $00)
	  copperEntry(221, c64lib.IRQH_BORDER_BG_0_COL, GREY, $00)
	  copperEntry(227, c64lib.IRQH_BORDER_BG_0_COL, DARK_GREY, $00)
	  copperEntry(232, c64lib.IRQH_BORDER_BG_0_DIFF, RED, BLUE)
	  copperEntry(252, c64lib.IRQH_BORDER_COL, LIGHT_BLUE, 0)
	  copperEntry(257, c64lib.IRQH_JSR, <custom1, >custom1)
	  copperLoop()
	}
	
I will explain how to use copper64 in my [next](copper64-2) post.

## References
* \[1\] [http://codebase64.org/doku.php?id=base:handling_irqs_with_some_simple_macros][1]
* \[2\] [http://codebase64.org/doku.php?id=base:stable_raster_routine][2]

[1]: http://codebase64.org/doku.php?id=base:handling_irqs_with_some_simple_macros
[2]: http://codebase64.org/doku.php?id=base:stable_raster_routine