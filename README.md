# PDP-10 KT20 Minnow

In 2009, I started a to think about an FPGA-based implementation of the KT20 Minnow.  This was my first attempt at a non-trivial VHDL
project and I spent some time on this design before I decided to move to something simpler.  Eventually I created a PDP-8 in VHDL and
then started the KS10 FPGA in Verilog.

This repository was created to archive this information.  Maybe someone will find it useful in the future.

Regards,

Rob Doyle (doyle at cox dot net)

## What was Minnow?

The PDP-10 Minnow was a program at DEC that was aimed at creating a desktop or deskside version of the popular PDP-10.  Minnow was cancelled
during development and was never completed.  It is not clear to me how close to a completed product this was.  Various people (who would know...)
have reported status on the project.  Dan Murphy (dlm) claimed that he ported TOPS-20 to it and it ran well enough to "demonstrate some exec
mode programs".  See [this post](https://hack.org/mc/retro-computing/texts/minnow.txt) for details.  David Lyons claimed he ported ITS to the Minnow.

It is not clear if the archived documentation was the most complete version of the documentation.

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

## Why did I stop working on Minnow?

A bunch of reasons.

1. As I mentioned previously, it was a bigger project than I was prepared to tackle.
2. I was concerned that this project as archived was incomplete and may never work without a lot of new design work.
3. In the end I found the design was not very interesting.  The Minnow hardware is essentially a generic 12-bit processor
   with a 36-bit ALU and 36-bit registers; with a layer microcode on top.  Because the micro-architecture doesn't really
   resemble a PDP-10, every operation requires the micro-machine to execute a pile of micro-instructions to simulate the
   PDP-10 processor.
4. I decided that the KS10 was a better target.

## Machine readable Minnow Microcode

I OCR'd the Minnow Microcode from the PDF archive format obtained from bitsavers as described above.
Most of the obvious translation mistakes have been cleaned up with a whole lot of hand editing.

[partially edited microcode (334KB)](https://github.com/KS10FPGA/Minnow/blob/main/wiki/minnow_v1.mic)<br>
[partially edited microcode (334KB)](https://github.com/KS10FPGA/Minnow/blob/main/wiki/minnow_v2.mic)

I think the first version of the microcode is an early translation.  I think the second version of
the microcode has a bunch of fixes - it also has some intentional changes.  It looks like the intentional
changes are related to me hating the assembler syntax with respect to octal constants.  It also looks
like there were some changes to debug the assembler that were supposed to be removed eventially.

I can't remember.

## The MICRO2 Assembler

No working copy of the MICRO2.EXE microcode assembler is known to exist.  It was used on this PDP-10,
some of the PDP-11s, and some of the early VAXen.  As best as I can tell, this tool was hosted on VAX
systems.   If you have a copy please let me know.  I’d like to get a microcode baseline (at least)
on a known working toolset.

The assembler was written using 'flex' and 'bison' as part of the parser.

If I recall correctly, the assembler works by parsing the microcode, converting the MICRO2 macro syntax
into regular expressions, and then repeatedly expanding regular expressions until the line of code is primitive.

[My version of MICRO2 microcode assembler (2.6MB)](https://github.com/KS10FPGA/Minnow/blob/main/wiki/asm27.tgz)

It sorta worked.

[Microcode listing file (1027KB)](https://raw.githubusercontent.com/KS10FPGA/Minnow/main/wiki/minnow.lst)<br>
[Microcode VHDL ROM File (310KB)](https://github.com/KS10FPGA/Minnow/blob/main/wiki/minnow.vhd)

## VHDL RTL Code

I don't remember much about the status of the VHDL code.  It is archived here.

[Minnow VHDL (61 MB)](https://github.com/KS10FPGA/Minnow/blob/main/wiki/vhdl.tgz)

## Minnow Processor Manual

[Minnow Manual V7 (964K)](https://github.com/KS10FPGA/Minnow/blob/main/wiki/MinnowMan7.pdf)<br>
[The Problem with Minnow RD01 (147K)](https://github.com/KS10FPGA/Minnow/blob/main/wiki/The%20Problem%20with%20Minnow%20RD01.pdf)



