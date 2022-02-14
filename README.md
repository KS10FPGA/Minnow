# PDP-10 KT20 Minnow

In 2009, I started a to think about an FPGA-based implementation of the KT20 Minnow.  I spent some time on the  before I decided to move to something simpler.
This repository was create to archive this information.

## What was Minnow?
The PDP-10 Minnow was a program at DEC that was aimed at creating a mini-computer (desktop?) sized version of the PDP-10.  Minnow was cancelled
during development and was never completed.  It is not clear to me how close to a completed product this was.  It is also not clear to me whether
the archived documentation was the most complete version of the documentation.

## What DEC Documentation of Minnow exists?

Not much.  A barely readable [schematic](http://www.bitsavers.org/pdf/dec/pdp10/KT20_Minnow/minnow_Schems_1979.pdf) and [microcode listing](http://www.bitsavers.org/pdf/dec/pdp10/KT20_Minnow/minnow_uCodeSrc.pdf) has been archived on [bitsavers.org](http://www.bitsavers.org).

## Minnow Guts?

The Minnow design is a fairly standard computer built using AMD 29xx processing elements.  The ALU uses nine 4-bit AM2903 “Super Slices”,
nine 4-bit AM29705 dual-port register expansions, and an AM2904 Status and Shift control unit.  The microsequencer uses an AM2910
micro-program controller.  Interrupts are handled by an AM2914 Vector Priority Interrupt Controller.  The PDP-10 instructions are
decoded by a 512 x 96 bit Dispatch ROM – one entry for each instruction.  A 4096 x 72 bit Control ROM containing the Microcode controls
the various parts of the Minnow micro-architecture.

When I first saw the Minnow micro-architecture, I knew it would be relatively straight-forward to re-implement the AMD bit-slice design
in a Field-Programmable Gate Array (FPGA).

## Machine readable Minnow Microcode

I OCR'd the Minnow Microcode from the PDF archive format obtained from bitsavers as described above.
Most of the obvious translation mistakes have been cleaned up with a whole lot of hand editing.

[partially_edited microcode](https://github.com/KS10FPGA/Minnow/wiki/minnow_v1.mic)
[partially_edited microcode](https://github.com/KS10FPGA/Minnow/wiki/minnow_v2.mic)

## The MICRO2 Assembler

No working copy of the MICRO2.EXE microcode assembler is known to exist.  It was used on this PDP-10,
some of the PDP-11s, and some of the early VAXen.  As best as I can tell, this tool was hosted on VAX
systems.   If you have a copy please let me know.  I’d like to get a microcode baseline (at least)
on a known working toolset.

[My version of MICRO2 microcode assembler](https://github.com/KS10FPGA/Minnow/asm27.tgz)

## VHDL RTL Code

