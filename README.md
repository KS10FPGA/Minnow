# PDP-10 KT20 Minnow

In 2009, I started a to think about an FPGA-based implementation of the KT20 Minnow.  I spent some time on the  before I decided to move to something simpler.
This repository was create to archive this information.

## What was Minnow?
The PDP-10 Minnow was a program at DEC that was aimed at creating a mini-computer (desktop?) sized version of the PDP-10.  Minnow was cancelled during development and was never completed.  It is not clear to me how close to a completed product this was.  It is also not clear to me whether the archived documentation was the most complete version of the documentation.

## What Documentation of Minnow exists?

Not much.  A barely readable [schematic](http://www.bitsavers.org/pdf/dec/pdp10/KT20_Minnow/minnow_Schems_1979.pdf) and [microcode listing](http://www.bitsavers.org/pdf/dec/pdp10/KT20_Minnow/minnow_uCodeSrc.pdf) has been archived on [bitsavers.org](http://www.bitsavers.org).

## Minnow Guts?

The Minnow design is a fairly standard computer built using AMD 29xx processing elements.  The ALU uses nine 4-bit AM2903 “Super Slices”,
nine 4-bit AM29705 dual-port register expansions, and an AM2904 Status and Shift control unit.  The microsequencer uses an AM2910
micro-program controller.  Interrupts are handled by an AM2914 Vector Priority Interrupt Controller.  The PDP-10 instructions are
decoded by a 512 x 96 bit Dispatch ROM – one entry for each instruction.  A 4096 x 72 bit Control ROM containing the Microcode controls
the various parts of the Minnow micro-architecture.

When I first saw the Minnow micro-architecture, I knew it would be relatively straight-forward to re-implement the AMD bit-slice design
in a Field-Programmable Gate Array (FPGA).

Implementation Status:
<ol>
    <li>
        I’ve started writing a Processor Manual for the Minnow design.  It is very preliminary but tries to describe compatibility between
        the Minnow Design and the various completed and never completed PDP-10 designs.
    </li>
    <li>
        Hardware Design. The early development will use the Xilinx SP601 Evaluation board. The eval board looks like it has everything
        that I need on it  and is fairly inexpensive at $295.00.  The SP601 is USB programmable, has an Elpida 1G-bit (64Mx16) DDR2 RAM,
        Serial UART to USB adapter, 10/100/1000 Ethernet MAC, some buttons and switches.  I wish the RAM  was x18 instead of x16 - so,
        as I suspected, I'll be using a 9-bit  wide RAM interface. Even then, the DDR2/800 MHz memory interface will fetch a 36-bit word
        about every 5 nano-seconds. The final version will use a Xilinx XC6SLX9-2TQG144 (when available) in a custom printed wiring board.
    </li>
    <li>
        Firmware Design. This PDP-10 is implemented in VHDL [proc.tgz  (7.5 MB)]: (rev 0.6).  The design is broken into the following
        sections which pretty closely map to the Minnow schematic implementation.
    </li>
    <ol>
        <li>
            Microsequencer. The Microsequencer is fairly well tested, and has been place & routed at 125 MHz. The Microsequencer consists of the following large blocks:
        </li>
        <ol>
            <li>AM2910 micro-program controller</li>
            <li>Control ROM</li>
            <li>Dispatch ROM</li>
        </ol>
        <li>
            Arithmetic Logic Unit (ALU).  The ALU has been place & routed at 75 MHz and is currently implemented exactly like the AM29xx version -
            without any pipelining - and the performance shows it. Pipelining would help, but doing the simple things would be incompatible with
            the existing microcode.  Pipelining would also allow me to use the built-in DSP48A1 slices - yielding a 4 nanosecond 18x18 add or
            multiply.  Four of these slices would yield a 36x36 multiply or add in a few clock cycles. Ultimately, the ALU will need to be pipelined.
            The ALU consist of the following large blocks:
        </li>
        <ol>
            <li>
                AM2903 “Super Slices”- Actually the ALU is coded as if the ALU was 36 bits wide (no 4-bit slices).
                When the ALU was coded using 4-bit slices the synthesis tool was unable the figure out that the 4-bit
                pieces were cascaded and generated terrible code.  The Xilinx FPGA has special hardware for the
                serial carry between adder sections.  I could not contort the VHDL code to use the serial carry between slices.
                Plus it’s actually clearer what going on inside when the ALU is implemented 36-bits wide.
                Lastly the AM2903 register file was doubled in size eliminating the need to have external AM29705 dual-port
                register expansions
            </li>
            <li>
                AM2904 Status and Shift Control Unit – The AM2904 connects to the LS and MS bits to the ALU and is responsible
                for controlling shift and rotate operations of the ALU.
            </li>
        </ol>
        <li>Clock – The Clock package generates all the clock signals for the design.</li>
        <li>Flags – The Flags package contains the processor Flags Register.</li>
        <li>Interrupts – Interrupts are sampled by the microcode.  The Interrupt Controller’s ability to vector to a dispatch routing is not used.</li>
        <ol>
        	AM2914 Interrupt Controller – The Interrupt Controller is used to mask and unmask interrupts and to determine the priority of the highest interrupt, if any.
        </ol>
        <li>Memory Interface – No implemented, yet.</li>
        <li>Pager – This needs some work.  I still don’t completely understand PDP-10 extended paging.</li>
        <li>Processor Registers</li>
        <li>
            RAM File- The RAM File contains the microcode scratch space, the 8 AC blocks, and the Page Table.
            I’m not exactly sure how the Page Table stuff should work.  See Section 8 of the Processor Manual.  
        </li>
        <li>
            Test Condition Data Selector – The Microsequencer has a single input which can be tested and used for conditional jumps,
            conditional calls, and conditional returns.  This is a big multiplexer, controlled by the microcode,  that selects one
            of many register or status signals for the microsequencer.
        </li>
        <li>Pager – This needs some work.  I still don’t completely understand PDP-10 extended paging.</li>
        <li>Timer – This block contains the ... </li>
        <li>Pager – This needs some work.  I still don’t completely understand PDP-10 extended paging.</li>
        <li>
            UART IO – The UART IO section of Dionda design is significantly different than the Minnow design.
            The Minnow Design has full RS-232 hardware handshaking (with all the pins), supports external clocking
            of the UART Transmitters and Receivers and a multitude of RS-232 concepts that have gone the way
            of the dinosaur.  The UART also supports synchronous data transfers which is not implemented.
            For these reasons only the useful parts of the design are implemented.    
        </li>
        <ol>
            </li>SC2651 UART-  Such an oldie.  I believe that this UART was chosen from the many that
                 were available because it can generate independent interrupts for Transmit Data,
                 Receive Data, and line status change.  The eight UARTS, each with three interrupts,
                 all goes to a 24-bit priority encoder where the microcode samples UART status.
            </li>
            </li>
                Baud Rate Generator (BRG) – This package supplies the clocks to the SC2651 UART Transmitters and Receivers.
            </li>
        </ol>
        <li>DISK IO – The DISK IO emulates 8 TBD DEC Disk Drives using a single IDE PATA disk drive.</li>
    </ol>
    <li>A doxygen Reference Manual (FWIW) describing the VHDL design in detail is available from here.</li>
    <li>Microcode  Design (Minnow Microcode): [Minnow.mic.txt (0.33 MB)]: (rev 27)</li>
    <ol>
        <li>
            The Minnow Microcode has been OCR’d from the PDF archive format.  Most of the obvious
            translation mistakes have been cleaned up with a whole lot of hand editing.  I’ve added
            some comments to the code as I’ve read it and tried to understand it.
        </li>
        <li>
            No working copy of the MICRO2.EXE microcode assembler is known to exist.  It was used on this PDP-10,
            some of the PDP-11s, and some of the early VAXen.  If you have a copy please let me know.  I’d like
            to get a microcode baseline (at least) on a known working toolset.
        </li>
        <li>
            I’ve created my own microcode assembler to compile the Dionda microcode.  It works OK but could use
            a lot of work to be completely bullet-proof.  It is available here.
        </li>
    </ol>
    <li>
        The VHDL Synthesis, Place and Route, Bit Generation, and Simulation is all performed using the 
        Xilinix ISE 11.2 Webpack.  You’ll need to download version 11.1 and apply the version 11.2 update.
    </li>
</ol>
