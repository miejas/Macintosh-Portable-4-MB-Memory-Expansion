# Macintosh Portable 4 MB Memory Expansion
A 4 MB memory expansion for the Apple Macintosh Portable.

## In this repo
You will find several items, but most importantly there is:
* a Logisim .circ file that describes the address decoding and chip select logic
* a KiCad project folder with the schematics and PCB
* the original schematic for the Portable
* details about the revisions

## Logisim
As mentioned, this file describes the address decoding and chip select logic. It is relatively simple, but I'll try to explain it here too.

The memory module shares address space with the 1 MB onboard memory. That memory uses the first 19 bits of the address space (A1 to A19). So "our" memory must not drive the bus, unless A20 and/or A21 are active. And since the memory module is limited to 4 MB (or 22 bits of address space), it should NOT drive the bus when A22 and/or A23 are active (since that could be used by something else). This decision process is linked to the nRAM_ENA line (RAM_ENABLE* in the KiCad project). This line also disables much of the chips on the PCB.

Please note that there is actually only 21 bits of address space used for 4 MB. You'd think that would only result in 2 MB of addressable memory, but that is where LDS and UDS come into play. Each of those signals enables or disables an 8 bit slice of memory. In effect, this kind of adds an extra bit of address space, hence the full 4 MB.

The chip selection and mode (read or write) process is driven by four values:
* A21: selects the chip colum (U10+U11 or U12+U13)
* nLDS and nUDS: select the chip bank (U10+U12 or U11+U13 or both) to output either 8 bits or 16 bits of data
* RW: toggles the OE or WE pins on all the chips at once

Address lines A1 to A20 go directly to the SRAM, as well as the data lines.

## KiCad
Again, a relatively simple schematic. It is divided into 4 hierachical pages according to the different functions of the module. A warning when reading the decoding logic: KiCad (on my PC at least) does not have OR gates, but since they come in the same package as NOT gates, I've selected those in stead. But I'm really using OR gates, so the Logisim file is leading.

The PCB itself is designed in 4 layers with each layer having a plane (V_REF, GND, VCC and GND again) and many, many vias. The input/output handling is based off of the HCT-series of chips, although KiCad probably mentions otherwise in some cases. I might change that in the future. In fact, these are the chips that were selected so far:
* 74HCT132 (quad NAND gate)
* 74HCT32 (quad OR gate)
* 74HCT138 (decoder)
* 74HCT245 (address and data buffers) - the 74HCT244 might also work for the address buffers (warning: some manufactures make this an inverting version of the 245 and some a one-way version of the 245. Check the data sheets!)
* AS6C8008 (SRAM) - by Allied Semiconductor, but chips by ISSI, Cypress, Lyontek,... should work as well. The most import parameters are an access time of 55ns or less, a 1M x 8bit (address x data) layout and 5 V input voltage.

The HCT-series are chosen, because the mainboard on the portable uses the old TTL logic. The memory module uses more "modern" SRAM chips that probably won't work very well with those levels. That means CMOS levels for the output, but TTL levels for the input. That automatically puts you in the HCT-series.

## Revisions
__Rev. A__
* There is a race condition between the address buffer chips and the "ram enable" logic. A workaround is to tie pin 19 of chip U7 to gnd_ref (or ground). This will be fixed in board rev. B.
* A20 and A23 on the buffer chip only seem to go high when another ADDR-input on that chip is high. When A20 or A23 are the only one, they seem to hover around 1.2V instead of Vcc. No root cause found yet.

## Closing words
Since I have absolutely no experience in schematic and PCB design, there will probably be some errors and "donts". If you happen to find them, please let me know so I can adapt the design accordingly. Although I am getting closer, no working board exists today. Use without thorough examination is at your own risk!

No need to get my permission for personal or commercial use. Feel free to do with this project in any way you see fit. However, I'd appreciate this source being mentioned in some capacity and receive a small message when you base a product off of these files. Just to know how the project is being used :)

For discussion regarding issues or concerns or comment or .... please use the discussion section.
