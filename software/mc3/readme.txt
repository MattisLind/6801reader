
The mc3 monitor is developed by Daniel Tufvesson and our modifications are based on version 1.4.1
The original source code can be fetched here: http://www.waveguide.se/?article=mc3-monitor-141

In order to prepare the monitor you need to run it though a cross assembler, the assembler syntax
is supported by a package developed E.J Rupp and ported to IBM PC back in 1987. We have included
a port to Linux by Daniel in this repository for convinience but is also available at his page
http://www.waveguide.se/?article=68xx-cross-assembler

To build the binary and prepare it for the eprom just do the following:

$ ../motasm_linux/as1 mc3-6801rdr-1.4.1.asm

it will generate a file mc3-6801rdr-1.s19, note that the name is trunkated. This is old software,
don't try longer names than the original it will generate errors, but this is fine.

NOTE: You can also use the mc3-6801rdr-1.s19 file we have provided and just prom it. :)

If you know already how to manipulate the .S19 file and prepare it for the EPROM you can stop read here

If you want to get rid of empty fluff in the .s19 file we can use the srec_cat from the SRecord package
by Peter Miller available on Linux. If you don't have it already just install it:

$ sudo apt-get install srecord

It also have an srec-info utility to tell us about the contents of the s19 file

$ srec_info mc3-6801rdr-1.s19 
Format: Motorola S-Record
srec_info: mc3-6801rdr-1.s19: 1: warning: no header record
Execution Start Address: 00000000
Data:   F000 - F77B
        F7EE - F7EF
        F7F2 - F7FF

We can see that the start address is 0000000 which means that the linker put in a
lot of fluff between that start address and the real start address. We can also see
that the vector area are displaced at F7EE-F7FF but don't despair the 6801 reader
hardware will map in those vectors at reset so they will appear at FFEE-FFFF so that
execution will jump into the right place.

To de-fluffify the s19 file and convert it to a binary:

$ srec_cat mc3-6801rdr-1.s19 -offset -0xf000 -o mc3.bin -binary

Check out the size of mc3.bin:

$ ls -l mc3.bin
-rw-rw-r-- 1 joakim joakim 2048 aug 30 21:30 mc3.bin

We can see that it will fit the 2KB area F000-F7FF. Pad it, load it and burn it!

