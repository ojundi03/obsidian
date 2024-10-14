### Emu Dev Guide
- [ Guide to Chip-8 Emulator Development](https://tobiasvl.github.io/blog/write-a-chip-8-emulator/)
- [C++ Specific Guide](https://austinmorlan.com/posts/chip8_emulator/)
 
 
- The CHIP-8, created in 1977, is an interpreted programming language. It was designed to ease game development for the COSMAC VIP kit computer. Nowadays, it is essentially the "Hello World" of emulator development. 
- Though CHIP-8 is not a game console itself, the development of an emulator for it is a similar process, leading to it being a good starting point in understanding the world of console emulation development. 

# Storage in memory
- Each Instruction is exactly 2 bytes long
- Programs should be loaded to memory starting at address `0x200`
- `0x000` and `0x1FF` are reserved for the interpreter. (Some computers require it to be loaded at a different starting address, but this is not the norm)
- The final 352 bytes of memory are reserved for "variables and display refresh", not to be used in programs.
- In the COSMAC VIP systems, which have 2048 bytes of ram, this gives a range of `0x200`to `0x69F` to be used by programs. 
# Registers
## Data registers
- The CHIP-8 interpreter defines sixteen general purpose registers, one for each HEX digit (`V0 to VF`). 
- Each register is 8 bits in length
- the data registers are the primary means of data  manipulation provided by the chip-8 language. 
- Using instructions, these registers can be loaded with values, added, subtracted, etc. 
- While any register can be used for data manipulation, the `VF` register is often modified by certain instructions to act as a flag
## Address register
- A 16 bit address register, `I`, is used with operations related to reading and writing memory. Though it is sixteen bits wide, it can only be loaded with a 12-bit memory address due to range of memory accessible to CHIP-8 instructions

# Subroutines
- the CHIP-8 interpreted must guarantee sufficient stack space for up to twelve sucessive subroutine calls. Modern implementations may allocate enough for over 12.
# Timers
The chip-8 provides two programmable timers, `delay time`, and `sound timer`
- Similar to a data register, a timer can be loaded with an unsigned 8-bit value.
- Instructions `FX15` and `FX18` are used to set the values of the delay timer and sound timer respectively
- When a timer is set to a non-zero value, it will count down at 60hz until 
- When the sound timer's value is non-zero, a tone will be emitted from the speaker. The frequency and waveform are unspecified. 
- The minimum value for a response is `0x02`. 
# Input
 - the interpreter will accept input from a 16-key keypad. Each key is a hex digit.
# Graphics
- The interpeter allows mono output at 64 x 32. The top left screen has coords (0x00, 0x00). The Bottom right is (0x3F, 0x1F).
- On the COSMAC, an "on" pixel with value `0x0` is black, while `0x1` is white. 
- Sprites are drawn with the `DYXN` instruction.
- They are rendered using XOR mode; when a request is processed to draw, the sprite data is XOR'd with the current graphics data of the screen.
- If the program attempts to draw out of range, the X value is reduced modulo 64. 
- Similarly, for Y it is reduced reduced modulo 32. 
- Sprited drawn part off-screen will be clipped.

# Fonts

Font data can be found [here](https://github.com/mattmikolay/chip-8/wiki/CHIP%E2%80%908-Technical-Reference#fonts)