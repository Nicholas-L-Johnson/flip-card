## Firmware
This is where all the logic lies for running the screen and reading the accelerometer.

Everything here centers around the Embassy framework.  Embassy is by far the easiest way to get into embedded rust.

Most of the complexity here stems from the need to drive a charlieplexed LED array.  The PIO peripheral is a godsend for this, it lets the data stream directly into GPIO signals without any use of the main 2 cores.

Speaking of performance, the simulation uses both cores and overclocks them to 200MHz.  The board runs at 40mA, and most of that is the controller, not the LEDs.  The lowest power mode available for the rust pac is the DORMANT state, which only brings the power usage down to 1.5mA.

That's really the only TODO here.  The RP2350 has some wonderful support for power domains, but the rust crates just don't support them yet.  Right now the card only lasts about 2-3 days in sleep mode before it needs charged again.

## Build and Install

Installing the firmware onto the card is pretty straightforward.  There are two pre-requisites that must be met first:

1) Because the card uses the Raspberry Pi Pico 2 (RP2350) you must have Picotool installed in order to load firmware.  You can find Picotool here: https://github.com/raspberrypi/picotool
2) The card must mount a volume when connected to your computer (it should do this by default for new cards)

Once those pre-requisites are satisfied, you can install the firmware by simply running:
```
cargo run --release
```
That will compile the binary, and then use picotool to load the binary onto the RP2350.


## OPTIONAL

After flashing with the firmware, you can only get it back into a flashing mode by using a wire to jump between the test points on the card.  However, if you would like for this to happen by holding the card upside down for 5 seconds you can do that with a minor code change.

In main.rs, uncomment line 780 and comment out line 781, THEN run the build and install steps above. 


