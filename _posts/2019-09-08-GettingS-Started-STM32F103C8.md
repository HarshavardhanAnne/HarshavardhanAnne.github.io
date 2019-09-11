---
layout: post
title: Getting Started with the STM32F103C8 - Part 1
---

The STM32F103C8T6, code named "Blue Pill", is a microcontroller developed by ST Microelectronics as part of their ARM cortex line of processors. Blue Pill is based on the ARM Cortex M3, with a 72 MHz max frequency, 64 KB flash, 20 KB SRAM, 37 GPIO pins, and lots of other features. For more information on this processor, here's the <a href = "https://www.st.com/resource/en/datasheet/stm32f103c8.pdf">datasheet</a> fromt ST. 

<p align="center">
    <img width="70%" height="70%" src="/images/getting_started_stm32f103c8/datasheet1.png">
    <img width="70%" height="70%" src="/images/getting_started_stm32f103c8/datasheet2.png">  
</p>

## What You'll Need
* STM32F103C8T6
* Micro usb cable
* USB to TTL Serial cable
* Soldering iron
* Linux experience

## Black Magic Probe
BMP is a JTAG & SWD debugging tool designed for ARM microcontrollers. The BMP retails for around $70, however we can make our own using an STM32F103C8T6. This is optional, however you will need to either buy a BMP or ST-Link V2 programmer. In this tutorial we will be creating our own programmer, as it is cheaper and relatively easy to implement. 

<p align="center">
    <img width="60%" height="60%" src="/images/getting_started_stm32f103c8/bmp_wiring_diagram.png">
</p>
<p align="center">
    <font size="3">
        Fig 1: Wiring diagram <a href="https://medium.com/@paramaggarwal/converting-an-stm32f103-board-to-a-black-magic-probe-c013cf2cc38c">(source)</a>
    </font>
</p>

The Blue Pill comes default with a USART bootloader, which allows us to program it using a USB to TTL serial adapter. The micro usb onboard does not work for programming unfortunately, as the bootloader is different. For more information on programming the device via micro usb, look up the stm32duino project as well as <a href="https://github.com/rogerclarkmelbourne/STM32duino-bootloader">https://github.com/rogerclarkmelbourne/STM32duino-bootloader</a>.

There are two yellow jumpers on the board labeled BOOT0 and BOOT1 as shown in the wiring diagram above. Default mode of 00, the microcontroller uses its own flash memory bootloader, which currently doesn't exist. To program the device over USART, move the BOOT0 jumper to 1 and BOOT0 to 0. That is, move the BOOT0 jumper to the right and leave the BOOT1 jumper the same. Now plug in the serial adapter into your PC. 

Next, we will download the required files and sources in order to flash the device. I am using Ubuntu 18.04 LTS.

```shell
sudo add-apt-repository ppa:team-gcc-arm-embedded/ppa
sudo apt update
sudo apt install gcc-arm-embedded
sudo apt install python-pip
sudo apt install python-serial
sudo apt install dfu-util
cd ~
git clone https://github.com/jsnyder/stm32loader.git
sudo apt install linuxbrew-wrapper

git clone --recursive git@github.com:blacksphere/blackmagic.git
cd blackmagic
make
cd src
make clean && make PROBE_HOST=stlink
```

Now we are ready to flash. Lets test the connection to make sure the device is working and communicating properly. Navigate to your default directory and go into the stm32loader directory. Then run the python script:

```bash
cd ~
cd stm32loader
sudo python stm32loader.py -p /dev/ttyUSB0
```

```bash
Can't init. Ensure that BOOT0 is enabled and reset device
Traceback (most recent call last):
  File "/home/harsh/development/blue_pill_dev/stm32loader/stm32loader.py", line 436, in <module>
    bootversion = cmd.cmdGet()
  File "/home/harsh/development/blue_pill_dev/stm32loader/stm32loader.py", line 127, in cmdGet
    self._wait_for_ask("0x00 end")
  File "/home/harsh/development/blue_pill_dev/stm32loader/stm32loader.py", line 80, in _wait_for_ask
    raise CmdException("Can't read port or timeout")
__main__.CmdException: Can't read port or timeout
```

If you get the following output above, its probably a timing issue. Double check your connections and ensure the Blue Pill is getting power. Now click the reset button on the board right before you execute the python script. Timing will be tricky, but you should see the following output if successful:

```bash
Bootloader version 22
Chip id: 0x410 (STM32 Medium-density)
```

Success! Now lets flash the blackmagic_dfu binary. Navigate to the blackmagic directory and into the src folder:

```bash
cd ~
cd blackmagic/src/
sudo python ~/development/blue_pill_dev/stm32loader/stm32loader.py -p /dev/ttyUSB0 -e -w -v blackmagic_dfu.bin
```

Execute the python script using the above command, and make sure to hit the reset button right before executing it. 

If successful, you will see the following output:
```bash
Bootloader version 22
Chip id: 0x410 (STM32 Medium-density)
Write 256 bytes at 0x8000000
Write 256 bytes at 0x8000100
...
Write 256 bytes at 0x8001C00
Write 256 bytes at 0x8001D00
Read 256 bytes at 0x8000000
Read 256 bytes at 0x8000100
...
Read 256 bytes at 0x8001C00
Read 256 bytes at 0x8001D00
Verification OK
```

Now we can move the BOOT0 jumper back to 0, and hit the reset button. Unplug the usb/ttl adapter and remove the connecting from it to the Blue Pill. Now connect a micro usb cable to the board and your PC. Right after plugging in, you can run the command "dmesg | tail" and you should see something similar:

```bash
[97280.433062] usb 3-3: USB disconnect, device number 27
[97280.433203] pl2303 ttyUSB0: pl2303 converter now disconnected from ttyUSB0
[97280.433216] pl2303 3-3:1.0: device disconnected
[97287.548052] debugfs: Directory '03' with parent 'devices' already present!
[97287.676017] usb 3-3: new full-speed USB device number 28 using xhci_hcd
[97287.847006] usb 3-3: New USB device found, idVendor=1d50, idProduct=6017, bcdDevice= 1.00
[97287.847008] usb 3-3: New USB device strings: Mfr=1, Product=2, SerialNumber=3
[97287.847010] usb 3-3: Product: Black Magic (Upgrade) for STLink/Discovery, (Firmware v1.6.1-352-g3a6947a)
[97287.847011] usb 3-3: Manufacturer: Black Sphere Technologies
[97287.847012] usb 3-3: SerialNumber: CFC6ACDD
```

Lets make sure the device is being recognized. Type "lsusb" into the terminal:

```bash
Bus 003 Device 028: ID 1d50:6017 OpenMoko, Inc. 
```
You should see the above line. 

Now run the command "sudo dfu-util -l", and you should see the following output:

```bash
Found DFU: [1d50:6017] ver=0100, devnum=28, cfg=1, intf=0, path="3-3", alt=0, name="@Internal Flash   /0x08000000/8*001Ka,120*001Kg", serial="CFC6ACDD"
```

Now run the following command "sudo dfu-util -d 1d50:6018,:6017 -s 0x08002000:leave -D blackmagic.bin" and you should see the output:

```bash
dfu-util 0.9

Copyright 2005-2009 Weston Schmidt, Harald Welte and OpenMoko Inc.
Copyright 2010-2016 Tormod Volden and Stefan Schmidt
This program is Free Software and has ABSOLUTELY NO WARRANTY
Please report bugs to http://sourceforge.net/p/dfu-util/tickets/

dfu-util: Invalid DFU suffix signature
dfu-util: A valid DFU suffix will be required in a future dfu-util release!!!
Opening DFU capable USB device...
ID 1d50:6017
Run-time device DFU version 011a
Claiming USB DFU Interface...
Setting Alternate Setting #0 ...
Determining device status: state = dfuIDLE, status = 0
dfuIDLE, continuing
DFU mode device DFU version 011a
Device returned transfer size 1024
DfuSe interface name: "Internal Flash   "
Downloading to address = 0x08002000, size = 78964
Download	[=========================] 100%        78964 bytes
Download done.
File downloaded successfully
Transitioning to dfuMANIFEST state
```

Now lets test everything out. If you are running Ubuntu 18.04, you will need to download arm-none-eabi-gdb and install it.
Go to the following <a href="https://packages.ubuntu.com/xenial/gdb-arm-none-eabi">link</a> and download the file for your architecture. Now go download libreadline6 <a href="https://packages.ubuntu.com/xenial/libreadline6">here</a>.
Now lets install the packages:

```bash
cd ~/Downloads
sudo dpkg -i libreadline6_6.3-8ubuntu2_amd64.deb
sudo dpkg -i gdb-arm-none-eabi_7.10-1ubuntu3+9_amd64.deb
```

Now we can test out gdb.

```bash
sudo arm-none-eabi-gdb
(gdb) target extended-remote /dev/ttyACM0
Remote debugging using /dev/ttyACM0
(gdb) 
```

And it works!

<p align="center">
    <img width="80%" height="80%" src="/images/getting_started_stm32f103c8/pinout.png">
</p>

Now we can connect a second Blue Pill to our BMP device. Connect +3.3V to +3.3V, GND to GND, PB14 on the BMP to SWDIO on the second pill, and PA5 to SWCLK. Make sure both BOOT0 and BOOT1 jumpers on both boards are set to "0".

You can write your own blink sketch, or clone this repo <a href="github.com">here</a> and compile. You will get a .elf file that we will load onto the second Blue Pill.

Now lets confirm that the second Blue Pill is connected. Navigate to your blink sketch elf file and run the following commands:

```bash
sudo arm-none-eabi-gdb blink-plain.elf

(gdb) target extended-remote /dev/ttyACM0
Remote debugging using /dev/ttyACM0
(gdb) monitor swdp_scan
Target voltage: unknown
Available Targets:
No. Att Driver
 1      STM32F1 medium density M3/M4
(gdb) attach 1
Attaching to program: blink-plain/blink-plain.elf, Remote target
0x080001f8 in delay (delay=70067) at src/main.c:6
6	    while(delay) delay--;
(gdb) r
The program being debugged has been started already.
Start it from the beginning? (y or n) y
Starting program: /blink-plain/blink-plain.elf
```

If everything worked successfully, you should now see a green LED on the board blinking. If you wish, you can debug using gdb. 

Anyways, that concludes part 1 of getting started with and STM32F103C8T6 Blue Pill board. 

<p align="right">
    <br>
    <a href="/post/Resurrection-Shift-Light-Part-2/">
    Getting started with the STM32F103C8 - Part 2
    </a>
</p>

### Sources
https://medium.com/@paramaggarwal/converting-an-stm32f103-board-to-a-black-magic-probe-c013cf2cc38c<br>
https://gojimmypi.blogspot.com/2017/07/BluePill-STM32F103-to-BlackMagic-Probe.html<br>
https://acassis.wordpress.com/2018/12/27/adding-arm-none-eabi-gdb-to-ubuntu-18-04/<br>
https://github.com/jsnyder/stm32loader<br>
https://github.com/rogerclarkmelbourne/STM32duino-bootloader<br>
https://github.com/libopencm3/libopencm3<br>
https://github.com/blacksphere/blackmagic<br>
https://mcuoneclipse.com/2019/06/23/black-magic-open-source-debug-probe-for-arm-with-eclipse-and-gdb/<br>
https://darkdust.net/files/GDB%20Cheat%20Sheet.pdf<br>
https://cycling-touring.net/2019/05/flashing-and-debugging-non-development-stm32-boards<br>
