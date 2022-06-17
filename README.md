# Amiga clock port to Raspberry Pi interface

This interface has a small 64 kB SRAM memory that is shared between an Amiga and a Raspberry Pi.
The Amiga connects to the interface through the [clock port](https://en.wikipedia.org/wiki/Clock_port).

By adapting the [A314](https://github.com/niklasekstrom/a314/) software to this interface it should be possible to run services such as a314fs and the SANA-II network driver.

![Compact PCB](Docs/fit_shield_pcb.png?raw=True)

## Details about how the interface works

Either side, the Amiga or the Pi, can read from and write to the SRAM.
The interface is 8 bits wide, meaning that each access reads or writes one byte.
The interface is symmetric between the sides, so in this description we describe how the Amiga is communicating with the interface, but the communicating between the Pi and the interface works in the same way.

The Amiga can perform two commands towards the interface:

- Read a register
- Write a register

The interface exposes four registers:

- REG_SRAM = 0
- REG_IRQ = 1
- REG_A_LO = 2
- REG_A_HI = 3

The interface has the following state variables:

- Amiga address pointer, 16 bits
- Pi address pointer, 16 bits
- Amiga interrupt request, 1 bit
- Pi interrupt request, 1 bit

The actions that the Amiga can do, and their effects:

- Write REG_A_LO or REG_A_HI: update the Amiga address pointer.
- Write REG_IRQ: write 0 to clear the Amiga interrupt request bit, and write 1 to set the Pi interrupt request bit.
- Read REG_SRAM: reads and returns the byte in SRAM that the Amiga address pointer points to, and then increments the pointer.
- Write REG_SRAM: same as above but writes a byte to SRAM and increments address pointer.

So in order to read and write to the SRAM, either side must set the address pointer, and then repeatedly read (or write) REG_SRAM in order to read (or write) subsequent positions in the memory.
