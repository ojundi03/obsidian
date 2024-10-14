# CPU

## 6502
- The NES CPU core is based on the *MOS Technology 6502 Processor*, and runs at approx *1.79Mhz* (*1.66MHz* in the PAL NES)
- It was made by *Ricoh*, and notably lacks the *MOS6502*'s decimal mode
- The NTSC NES uses the **2A03**, while the PAL NES, the **2A07** is used.

## 2A03 and 2A07
- The **2A03**, short for *RP2A03*, is the common name for the NTSC NES CPU chip. It consists of *MOS Technology 6502 Processor* (lacking decimal mode), with audio, joypad, and DMA functionality.
- Pal systems use the similar **RP2A07**, which has a different clock rate, adjusted sampled audio rates, and DMA bugfixes
- The **2A07** was modified to better suit the 50Hz refresh rate of the PAL television standard, but most developers lacked the resources to adjust their games, leading to many PAL games sounding slower, lower pitched, and sometimes out-of-tune compared to the original release.

###  Register Map
- In addition to the registers in the 6502 Core, the 2A03 contains 22 memory-mapped registers for sound generation, joystick reading, and OAM DMA transferring.
- Unlike the addresses of PPU registers, CPU registers are completely decoded, meaning that the entire space from the end of CPU registers to the top of address space (`$4020` to `$FFFF`) is available to the game pak.
- The Range `$4018` to `$401F` does nothing on retail NES. It was Intended for **2A03** functionality that never made it to production. 
- Various revisions to the **2A03** include test registered (disabled in normal operation) or remnants of an incompletely implemented [IRQ counter](https://www.nesdev.org/wiki/RP2A03_Programmable_Interval_Timer) that was disconnected from the rest of the circuit. 
- Mappers can place writable registers here without conflicting with the **2A03**, but placing readable registers here should be avoided because of [conflicts with DMA](https://www.nesdev.org/wiki/DMA#Register_conflicts).
- A register map for the #2A03 can be found here:
	- [2A03 Registers](https://www.nesdev.org/wiki/2A03)

# APU
- The NES **APU** is the *audio processing unit* of the console, generating sound for games.
- It is implemented in the **2A03** and **2A07** chips. 
- Its registers are mapped in the range `$4000`-`$4013`, `$4015` and `$4017`

# PPU
- The NES **PPU** (*Picture Processing Unit*) generates a composite video signal, with 240 lines of pixels, designed to be received by a television. 
-  It has its own address space, which typically contains 10kb of memory: 8kb of ROM or RAM on the game pak (possibly more on with one of the common #mappers) to store shapes of background and sprite tiles, plus 2kb of RAM in the console to store a map or two. 
- Two separate, smaller address spaces hold:
- a **palette**, which controls which are associated to various indices
- **OAM** (*Object Attribute Memory*), which stores the position, orientation, shape and color of the sprites, or independent moving objects. 
- These are internal to the PPU itself, and while the palette is made of *static memory*, the OAM uses *dynamic memory* (which will slowly decay if the PPU is not rendering)

# Mappers
- NES games come in cartirges, and inside of those cartidges are various circuits and hardware. Different games use different circuits and hardware, and the configuration and capabilities of such carts are called their **Mapper**.
- Mappers are designed to extend the system and bypass limitations, such as by adding RAM or even extra sound channels
- More commonly though, they allow games to be larger than 40Kb.
- The term arises from the concept of *memory mapping*, translating memory hardware into the *CPU* and *PPU*'s address spaces. 
- This memory map describes which addresses correspond to which physical locations of memory. 