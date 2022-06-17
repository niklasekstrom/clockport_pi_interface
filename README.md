# Amiga clock port to Raspberry Pi interface

This interface has a small 64 kB SRAM memory that is shared between an Amiga and a Raspberry Pi.
The Amiga connects to the interface through the [clock port](https://en.wikipedia.org/wiki/Clock_port).

By adapting the [A314](https://github.com/niklasekstrom/a314/) software to this interface it should be possible to run services such as a314fs and the SANA-II network driver.

![Compact PCB](Docs/fit_shield_pcb.png?raw=True)
