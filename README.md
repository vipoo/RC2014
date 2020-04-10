# RC2014 Emulator

This is an emulator for the RC2014 Z80 system.

The emulation includes
- Z80 CPU (time sync wants work)
- Standard RC2014 CPU card
- SC108 CPU card with 128K banked RAM and 32K ROM
- SC114 CPU card with 128K banked RAM and 32K ROM
- Easy-Z80 card with 512K banked RAM, 512K banked ROM and on board CTC and SIO
- Z80BSC/Z80SBC64 with 128K banked battery backed RAM and simple CPLD UART
- ROM and switchable ROM cards
- 6850 ACIA (narrow or wide decode)
- Z80 SIO/2
- 512K RAM/512K ROM card
- CF adapter
- DS1302 real time clock (time setting not supported)
- CTC at 0x88

At this point in time the serial emulation is complete and sufficient to run
the standard ROM environment with BASIC. This represents the basic
configuration of Z80 CPU card, 32K RAM card, 8K or switchable ROM card and
ACIA serial I/O card. This also corresponds to the RC2014 Mini.

With the right options and ROM image you can also boot CP/M on it via the
emulated compact flash adapter. This corresponds to the RC2014 with the
switchable ROM, 64K RAM card, ACIA card and Compact Flash card.

In 512K mode the emulator can run the ROMWBW 512K image for the RC2014
system.

To exit use ctrl-backslash

## To get actual hardware see

https://rc2014.co.uk/
https://smallcomputercentral.wordpress.com/
https://www.retrobrewcomputers.org/forum/index.php?t=msg&th=368&start=0&

## For ROM images see

https://github.com/RC2014Z80/RC2014/tree/master/ROMs/Factory
https://retrobrewcomputers.org/doku.php?id=software:firmwareos:romwbw:start
https://smallcomputercentral.wordpress.com/projects/small-computer-monitor/

## Usage

Options:
- -a		enable 6850 ACIA with usual RC2014 wide decode (80-BF)
- -A		enable 6850 ACIA with narrow decode (80-87)
- -b		512K ROM/512K RAM board
- -c		CTC card present (not yet tested)
- -d n		Turn on debug flags
- -e n		Execute ROM bank n (0-7) (not used with -b)
- -f		Fast mode (run flat out)
- -i path	Enable IDE and use this file
- -m board	Board type (z80 for default rc2014, easy-z80, sc108, sc114, z80sbc64,
z80mb64)
- -p		Pageable ROM (needed for CP/M)
- -r path	Load the ROM image from this path
- -s		Enable the SIO/2
- -R		Enable the DS1302 RTC
- -w		WizNET 5100 at 0x28-0x2B (works but buggy)

To build a disk image

./makedisk 1 my.cf

dd if=cf-image of=my.cf bs=512 seek=2 conv=notrunc

In other words the IDE disk format has a 1K header that holds
meta-data and the virtual identify block.

Compact flash images can be found at

https://github.com/RC2014Z80/RC2014/tree/master/CPM

Remember to unzip the image before putting it on the virtual cf card.

The Z80MB64 and Z80SBC are built around a battery backed RAM image rather
than a ROM. To start copy the Z80SBCLD.BIN file to your 'ROM' file and then
bootstrap as per the instructions. At any point when you use ctrl-\ to exit
the system will save the full memory image back to the ROM file (unless
marked read only), so you can do the full bootstrap. Alternatively you can
start from the SCM monitor or similar. These boards default to the bitbang
serial interface, unless an SIO or ACIA is specified.

# RBC (ex N8VEM) Mark 2 Emulator

This is a fairly basic emulator for the RBC v2 system board.

https://retrobrewcomputers.org/doku.php?id=boards:sbc:sbc_v2:start

Currently the following are emulated: 512K ROM, 512K RAM, memory mapping,
16x50 UART (minimally), 8255 PIO (PPIDE), 4UART (minimally), PropIOv2 (SD and
console), timer on serial hack but not yet ECB interrupt via serial hack.

The emulator also supports the RAMF card (not yet tested), a WizNET 5100 at
0x28-0x2B in indirect mode (buggy but works for socket 0 at this point),
and a debug port. Writing to 0xFD sets the debug value so you can trace
pieces of code.

The jumpers are not emulated. K7 is always assumed to be 1-2, K10 1-2 and
K11 1-2 giving a 32K/32K split.

## To get actual hardware see

https://retrobrewcomputers.org

## For ROM images see

https://retrobrewcomputers.org/doku.php?id=software:firmwareos:romwbw:start

## Usage

Options:
- -r path	path to ROM image
- -i path	path to IDE image (specify twice for 2 disks)
- -s path	path for PropIOv2 SD card
- -p		enable PropIOv2
- -t		enable timer hack
- -d n		set debug trace bits
- -f		fast (run flat out)
- -R		RAMFS ECB module (not yet tested
- -w		WizNET 5100 at 0x28-0x2B (works but buggy)

The sd card image is just a raw file of the blocks at this point.

# Grant Searle SBC Emulator

This is an emulator for the Grant Searle 9 chip CP/M computer. This is a
Z80 machine with 128K of RAM (64K accessible), 16K of boot ROM, dual serial
and a simple 8bit IDE CF interface.

## To build yourself an actual system see

http://searle.hostei.com/grant/cpm/

## For ROM images

Get the zip file from the web page above.

makebin -s 16384 ROM.HEX >searle.rom

You can build the CP/M bootable image and put it onto an IDE image using

cat hexFiles/CPM22.HEX | makebin -s 65535 >1.rom
cat hexFiles/CBIOS64.HEX | makebin -s 65535 >2.rom
dd if=2.rom of=1.rom bs=58880 skip=1 seek=1 conv=notrunc
dd if=1.rom of=3.rom bs=1 skip=53248
dd if=3.rom of=searle.cf bs=512 seek=2 conv=notrunc

cpmtools can then be used to manipulate it further.

## Usage

Options:
- -r path	path to ROM image (default searle.rom)
- -i path	path to IDE image (default searle.cf)
- -d n		set debug trace bits
- -f		fast (run flat out)
- -t		external 10Hz timer on DCD (not yet accurate to 10Hz)
- -b		A16 of RAM is controlled by UART RTS line

# Tom's SBC Version C

This is a close relative of the Grant Searle system except that it has up to
64K of banked ROM. This is handled by the searle emulator when provided with
a 64K or 32K ROM image. The only differences are the two ROM paging ports.
The ROM can be paged back in.

## To build yourself an actual system see

https://easyeda.com/peabody1929

## For ROM images

Use either the Grant Searle ROM padded to 32K or the SCM firmware at
https://smallcomputercentral.wordpress.com/projects/small-computer-monitor/

# LiNC 80 SBC

This is an emulator for the LiNC80 system with optional expanded memory. It
emulates the 64K ROM, 64K base RAM, optional banked RAM, memory mapping
control, Z80 SIO UART, Z80 CTC and onboard IDE. The PIO is only minimally
emulated.

Additionally a board mod to allow the top 32K to be banked is also supported
in emulation.

It has been tested with SCMon, CP/M and Fuzix.

## To get actual hardware see

http://linc.no

## For ROM images see

http://linc.no/products/linc80-sbc1/software-for-the-linc80/

## Usage

Options:
- -r path	path to ROM (default linc80.rom)
- -i path	path to IDE image (default linc80.ide)
- -b n		number of banks (must be a power of two, default 1 - unbanked)
- -d n		set debug trace bits
- -f		fast (run flat out)
- -x		emulate the 32K banking mod

# RC2014 8085

This is a model of the standard RC2014 system but fitted with Ben Chong
(ancientcomputing)'s 8085 processor card and optionally the 16550A uart
interface.

## To build yourself an actual system see

http://ancientcomputing.blogspot.com/

## For ROM images

The monitor ROM is available from Ben's github at
https://github.com/ancientcomputing/rc2014

## Usage
- -1		Enable 16550A UART
- -a		Enable 6850 ACIA with usual RC2014 wide decode (80-BF)
- -A		Enable 6850 ACIA with narrow decode (80-87)
- -b		512K ROM/512K RAM board
- -c		CTC card present (not yet tested)
- -d n		Turn on debug flags
- -e n		Execute ROM bank n (0-7) (not used with -b)
- -f		Fast mode (run flat out)
- -i path	Enable IDE and use this file
- -r path	Load the ROM image from this path
- -s		Enable the SIO/2
- -R		Enable the DS1302 RTC (clashes with 16550A UART)
- -w		WizNET 5100 at 0x28-0x2B (works but buggy)

# SmallZ80

An emulation for Terry Gulczynski's SmallZ80 system. This emulates the full
machine except for the optional floppy controller. Some details are not
emulated including EEPROM writeback and RTC clock setting.

The emulator is however good enough to boot the SmallZ80 system firmware,
run the supplied Z System and software off the supplied disk image and of
course to boot Fuzix.

## To build yourself an actual system

http://stack180.com/SmallZ80%20Services.htm

## For ROM and filesystem images

http://stack180.com/SmallZ80%20Downloads.htm

## Usage

Options:
- -r path	Path to ROM (default smallz80.rom)
- -i path	Path to IDE image (up to two)
- -d n		Enable debug tracing
- -f		Run flat out rather than at about native speed

# SBC-2G-512K

Closely related to the Grant Searle design but with banked memory. The
system has 512K of RAM, 16K of boot ROM, dual serial and a simple 8bit IDE
CF interface

## To build yourself an actual system see

https://retrobrewcomputers.org/doku.php?id=builderpages:rhkoolstar:sbc-2g-512

## For ROM images and file system images see

Get the ZIP file from the web page

makebin 10-HEXFILES/SMON35.HEX > sbc2g.rom

You can build an emulated drive from the image with

./makedisk 6 sbc2g.ide
dd if=System18.img of=sbc2g.ide bs=512 seek=2 conv=notrunc


## Usage

Options:
- -r path	path to ROM image (default sbc2g.rom)
- -i path	path to IDE image (default sbc2g.cf)
- -d n		set debug trace bits
- -f		fast (run flat out)
- -t		external 10Hz timer on DCD (not yet accurate to 10Hz)

# Z80 Membership Card

This is an emulation of the Z80 Membership Card full stack with 512K
of RAM. It emulates the full stack including the bitbang SPI/SD card
interface and is sufficient to run CP/M or Fuzix on the card.

The keypad and bitbang serial port are emulated as not being used. The LED
display is also not decoded and displayed.

## To build yourself an actual system see

http://www.sunrise-ev.com/z80.htm

## For ROM images and file system images see

http://www.sunrise-ev.com/z80.htm

Building an emulated drive image is a little complicated. You need to
partition the image to include a FAT16 MSDOS partition with the files on as
described. If you want to run Fuzix you also need to place the Fuzix loader
hex file on that partition and have another partition with Fuzix on it, as
Fuzix avoids the emulated floppy disk layer and drives the SD card directly.

## Usage

Options:
- -r path	path to ROM image (default z80mc.rom)
- -s path	path to SD image (default none)
- -d n		set debug trace bits
- -f		fast (run flat out)

# Z80 MBC2

This is a Z80 level emulation of the Z80-MBC2. The I/O processor interface
is emulated rather than actually emulating the I/O processor itself.
Firmware including timer and transmit queue size is emulated.

## To build yourself an actual sysem see

https://hackaday.io/project/159973-z80-mbc2-4ics-homemade-z80-computer

## For ROM images and file system images see

https://hackaday.io/project/159973-z80-mbc2-4ics-homemade-z80-computer

and hit "Files".

## Usage

Options:
- -s diskset	Disk set number 0-9
- -d n		Set debug trace bits
- -b path	Image for the IOS to load into Z80 RAM
- -i		Turn on interrupt emulation
- -f		Fast (run flat out)
- -a		Set load address of image (default 0)


# Building

> [![Build Status](https://travis-ci.org/vipoo/RC2014.svg?branch=dean/develop)](https://travis-ci.org/vipoo/RC2014)

`make`

`make install`

