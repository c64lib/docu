---
layout: page
title: mem
description: Various memory related operations
files: 
    - lib/mem.asm
    - lib/mem-global.asm
subroutines:
    - lib/sub/copy-large-mem-forward.asm
    - lib/sub/fill-mem.asm
    - lib/sub/fill-screen.asm
    - lib/sub/rotate-mem-right.asm
backurl: true
---
This module contains various memory related macros and subroutines that cover
following scenarios: copying memory blocks, rotating memory blocks, filling
memory blocks with given value.<!--more-->

{% include lib-module-usage-note.md %}
