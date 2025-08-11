## Firmware
This is where all the logic lies for running the screen and reading the accelerometer.

Everything here centers around the Embassy framework.  Embassy is by far the easiest way to get into embedded rust.

Most of the complexity here stems from the need to drive a charlieplexed LED array.  The PIO peripheral is a godsend for this, it lets the data stream directly into GPIO signals without any use of the main 2 cores.

Speaking of performance, the simulation uses both cores and overclocks them to 200MHz.  The board runs at 40mA, and most of that is the controller, not the LEDs.  The lowest power mode available for the rust pac is the DORMANT state, which only brings the power usage down to 1.5mA.

That's really the only TODO here.  The RP2350 has some wonderful support for power domains, but the rust crates just don't support them yet.  Right now the card only lasts about 2-3 days in sleep mode before it needs charged again.
