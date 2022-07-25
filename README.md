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

## Bill of materials

| References                     | Value                | Footprint                       | Quantity |
|--------------------------------|----------------------|---------------------------------|----------|
| C1, C2, C3, C4, C5, C6, C7, C8 | 0.1u                 | C_0603_1608Metric               | 8        |
| U1                             | XC9572XL-VQ64        | TQFP-64_10x10mm_P0.5mm          | 1        |
| IC2, IC3                       | SN74LVC573APWRE4     | SOP65P640X120-20N               | 2        |
| IC1                            | IS63WV1288DBLL-10TLI | SOIC127P1176X120-32N            | 1        |
| J1                             | Raspberry_Pi_2_3     | PinSocket_2x20_P2.54mm_Vertical | 1        |
| J2                             | Conn_02x11_Odd_Even  | PinSocket_2x11_P2.00mm_Vertical | 1        |
