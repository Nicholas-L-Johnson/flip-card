## Firmware
This is where all the logic lies for running the screen and reading the accelerometer.

Everything here centers around the Embassy framework.  Embassy is by far the easiest way to get into embedded rust.

Most of the complexity here stems from the need to drive a charlieplexed LED array.  The PIO peripheral is a godsend for this, it lets the data stream directly into GPIO signals without any use of the main 2 cores.

Speaking of performance, the simulation uses both cores running at 150 MHz.  The board runs at 40mA, and most of that is the controller, not the LEDs.  The lowest power mode available for the rust pac is the DORMANT state, which only brings the power usage down to 1.5mA.

That's really the only TODO here.  The RP2350 has some wonderful support for power domains, but the rust crates just don't support them yet.  Right now the card only lasts about 2-3 days in sleep mode before it needs charged again.

## Build and Install

Installing the firmware onto the card is pretty straightforward, just clone the whole repo into a folder and cd into the firmware folder.  There are two pre-requisites that must be met before flashing:

1) Because the card uses the Raspberry Pi Pico 2 (RP2350) you must have Picotool installed in order to load firmware.  You can find Picotool here: https://github.com/raspberrypi/picotool
2) The card must be connected via a usb-c cord (into the cutout on the board) and mount a volume when connected to your computer (it should do this by default for new cards, but read below for re-programming cards)

Once those pre-requisites are satisfied, you can install the firmware by simply running:
```
cargo run --release
```
(don't forget the --release or it will not work, and will need to be forced back to bootloader mode via the method below)

That will compile the binary, and then use picotool to load the binary onto the RP2350.

## Re-Programming an already programmed card

The card can be returned to bootloader mode (where it appears as a volume on a connected PC and can be re-flashed) in two ways.  

First, with the board powered, you can short the "GND" contact to the "BOOT" contact, then with that short in place, breifly short the "GND" contact to the "RUN" contact.  Then remove all the shorts and it should be in bootloader mode.

Second, the latest version of the code has a backup method. the card can be placed perfectly upside down (so all the fluid goes to the top of the screen) for 5 seconds, and that will kick it back to bootloader mode.  Note that the board will be stuck in that mode until the program is re-flashed, or the rp2350 loses all power.

## OPTIONAL

If you want to disable the backup method to go back to bootloader (becuse you don't want the card to ever accidentally go to bootloader mode) in main.rs, uncomment line 794 (the udf() function) and comment out line 793(the reboot() function), THEN run the build and install steps above.


