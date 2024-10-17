## sunxi-tools for c.h.i.p.

The exact sunxi tools version by NTC.

### Tools to help hacking Allwinner A10 (aka sun4i) based devices and possibly it's successors, that's why the 'x' in the package name.

fexc: .fex file (de)compiler

Usage: ./fexc [-vq] [-I <infmt>] [-O <outfmt>] [<input> [<output>]]

infmt:  fex, bin  (default:fex)
outfmt: fex, bin  (default:bin)

bin2fex: compatibility shortcut to call fexc to decompile an script.bin blob back into .fex format used by allwinner's SDK to configure the boards.

fex2bin: compatiblity shortcut to call fexc to compile a .fex file into the binary form used by the sun4i kernel.

fel: script interface for talking to the FEL USB handler built in to th CPU. You activate FEL mode by pushing the usboot/recovery button at poweron. See http://linux-sunxi.org/FEL/USBBoot for a detailed usage guide.

fel-gpio: Simple wrapper around fel-pio and fel to allos GPIO manipulations via FEL

fel-sdboot: ARM native sdcard bootloader forcing the device into FEL mode

fel-pio: ARM native helper for fel-gpio

pio: Manipulate PIO register dumps

nand-part: Tool for manipulating Allwinner NAND partition tables

jtag-loop.sunxi: ARM native boot helper to force the SD port into JTAG and then stop, to ease debugging of bootloaders.

bootinfo: Dump information from Allwinner boot files (boot0/boot1) --type=sd include SD boot info --type=nand include NAND boot info (not implemented)

phoenix_info: gives information about a phoenix image created by the phoenixcard utility and optionally extracts the embedded boot code & firmware file from their hidden partitions.

meminfo: Tool for reading DRAM settings from registers. Compiled as a static binary for use on android and other OSes. To build this, get a toolchain, and run: make CROSS_COMPILE=arm-linux-gnueabihf- meminfo

script-extractor: A simple tool, which can be executed on a rooted Android device to dump the script.bin blob from RAM via reading /dev/mem.

### Flashing notes

* If you flash CHIPs with the images contained in these backups they will still have the original NTC Debian package repositories configured. You will need to follow the instructions in the "package repository" page to repoint your CHIPs to this mirror.
* If you use "CHIP-tools" to flash your CHIP you will have to alter the location of the image repositories. Instructions are on the CHIP-tools flash images page.
* The "chip-update-firmware.sh" script will fail with newer versions of sunxi-tools, specifically the `fel` binary. Apparently the newer versions are incompatible with the older R8. I would recommend building from the NTC copy of the sunxi-tools repository. See the "NTC's GitHub repositories" page for the URLs. Then you can patch the "common.sh" from the "CHIP-tools" package to set the "FEL" variable (near the top) to the location of your freshly built "fel".
     
    A user created an all-in-one "singularity" image with all of the necessary software for flashing and the images to be flashed. You can find his guide on Reddit.

### New C.H.I.P. flashing method

I struggled to get the chip flashing to work as the code is incompatible with a modern linux system. So, I used apptainer to build an image.

If you want to use this method to flash your chip / pocketchip, see below.

1. Install apptainer or [singularity](https://singularityhub.github.io/singularityware-archive/docs/v2-3/index.html).

2. To download the .sif you can download it via the cli: `singularity pull --arch amd64 library://bpietras/bpietras/c.h.i.p-flasher:latest`

3. Get the right image for chip / pocketchip (see links below)

Then, if you want to flash a c.h.i.p.:

4. `singularity exec chip-flasher.sif chip-update-firmware.sh -L flash-collection/stable-server-b149` or, flash a pocketchip `singularity exec chip-flasher.sif chip-update-firmware.sh -L flash-collection/stable-pocketchip-b126`

5.  Once the command is entered, put the (pocket)c.h.i.p. in FEL mode with a bus wire (link FEL to GND). Use a data transfer type micro-usb cable to plug into USB (some cables are charging only). Then plug the CHIP into your PC.

#### Links

* If someone wants just the flash-collection/stable-server-b149 (chip, not pocketchip) they can get it from here: https://mega.nz/file/97phVRTB#s4e2FWfajnNf4qshi-0DzyTyshG4t7kGJfoLC5Hreqs

That's 0.4G rather than 4.8G - a much faster download than https://archive.org/details/C.h.i.p.FlashCollection

A commentor below pointed out http://chip.jfpossibilities.com/chip/images/stable - you can download the images you need there quickly.

EDIT - for ubuntu, the easiest way to install apptainer (works the same as singularity) looks to be: https://apptainer.org/docs/admin/main/installation.html#install-debian-ubuntu-packages

If you get a 'not in PATH' error, try running sudo sysctl -w kernel.unprivileged_userns_clone=1 then trying again.

Good luck chipperinos! 
