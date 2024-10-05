# ARM Processor Families
- **Cortex-A Series (application)**
	- High performance capable of full OS
- **Cortex-R Series(Real-Time)**
	- High Performance and reliability for real-time applications
- **Cortex-M Series (Microcontroller)**
	- Cost-sensitive solutions for deterministic microcontroller applications
- **SecurCore Series**
	- For high security applications
- **Classic**
	- Earlier classic processors such as ARM7, ARM9, ARM11
![[Pasted image 20241005140552.png]]

## ARM Processors vs ARM Architectures
- **ARM Architecture**
	- Describes the details of the instruction set, programmer's model, exception model, and memory map
	- Documented in the Architecture Reference Manual
- **ARM Processor**
	- Developed using one of the ARM architectures
	- More implementation details, such as timing information
	- Documented in processor's Technical Reference Manual
![[Pasted image 20241005140727.png]]

# Cortex-A
**Cortex-A Application Processors** are defined by the processor's ability to execute complex operating systems, such as Linux, Android, Chrome OS, Tizen, MS Windows, among others. This class of processors integrate a *Memory Management Unit (MMU)* to manage memory requirements of complex OS's and enable the download and execution of third party software.
### Examples
- Smartphones
- Feature Phones
- Tablets / eReaders
- Digital Telly
- Wearables
- Server/Enterprise
# Cortex-M
**Cortex-M** processors are the optimal solution for *low-power embedded computing applications*. The 32-bit Cortex-M processor family is the key to transforming all sorts of embedded systems into smart and connected systems. Often provided as a "black-box" with pre-loaded applications, they have limited capability to expand harware functionality and in most cases have no screen.
### Examples
- Merchant MCUs 
- Automotive Control Systems
- IoT
- Sensors
- Smart Meters
![[Pasted image 20241005140221.png]]

## Cortex-M processor Profile
- **M0**: Optimized for size and power (13 μW/MHz dynamic power)
-  **M0+**:** Lower power (11 μW/MHz dynamic power), shorter pipeline
-  **M3**: Full Thumb and Thumb-2 instruction sets, single-cycle multiply instruction, hardware divide, saturated math, (32 μW/MHz)
-  **M4**: Adds DSP instructions, optional floating point unit
-  **M7**: designed for embedded applications requiring high performance
-  **M23**, M33: include ARM TrustZone® technology for solutions that require optimized, efficient security
![[Pasted image 20241005141044.png]]

# RISC (Reduced Instruction Set Computer)
- a type of computer architecture that utilizes a streamlined set of simple and highly optimized instructions. By focusing on executing each instruction in a single clock cycle, RISC designs enhance processing speed and efficiency. This approach simplifies the processor design, allowing for easier pipelining and parallelism, which ultimately improves overall performance. RISC architectures are commonly used in various applications, including smartphones, tablets, and embedded systems, where speed and efficiency are crucial.

## Characteristics:
- 32-bit load/store architecture
- Fixed instruction length
- Fewer instructions than a CISC CPU (Complex Instruction Set Computer)
- Limited addressing modes, operand types
- Simple design easier to speed up, pipeline & scale