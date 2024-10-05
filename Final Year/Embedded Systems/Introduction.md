# The focus on Cyber-Physical Systems: Full of Contradictory Requirements

### Definition
- *Cyber-Physical Systems (CPS) are integrations of computational algorithms and physical components designed to monitor, control, and interact with the physical world in real time. These systems embody a seamless blend of **cyber** (software and networking) and **physical** (mechanical, electrical, or other tangible) elements, enabling sophisticated interactions and functionalities across various domains.*
- In cyber-physical systems

- Its Not Just about information technology  anymore, see:
	- Cyber + Physical
	- Computation + Dynamics
	- Security + Safety
- **Contradictions**
	- Adaptability vs Repeatability
	-  High performance vs. Low Energy
	• Asynchrony vs. Coordination/Cooperation
	• Scalability vs. Reliability and Predictability
	• Laws and Regulations vs. Technical Possibilities
	• Economies of scale (cloud) vs. Locality (fog)
	• Open vs. Proprietary
	• Algorithms vs. Dynamics
- **Innovation**
	- Cyber-physical systems require new engineering methods and models to address these contradictions

![[Pasted image 20241005132453.png]]

# Modelling, Design, Analysis

## Modelling
The process of gaining a deeper understanding of a system through imitation. Models express *what* a system does or should do
## Design 
The structured creation of artifacts. Specifying *how* a system does what it does
## Analysis
The process of gaining deeper understanding of a system through dissection, specifying *why* a system does what it does (or fails to do what the model outlines)


# Common topics, Why?

## Assembly
-  Assembly will help *understand how the processor works*
- Assembly runs faster than a high-level program. Performance critical codes must be written in assembly
	- Profiling tools can find bottlenecks and rewrite the code in assembly
	- Standard C programs do not use some operations available on ARM processors, such as `ROR (rotate right)` and `RRX (Rotate Right extended)`
- Hardware / Processor specific code
	- Processor Booting Code
	- Device Drivers
	- A test-and-set atomic assembly instruction can be used to implement locks and semaphores
- Cost-sensitive applications
	- Embedded devices, where code size is limited
- The best applications are written by those who've mastered ASM or fully understand the low-level implementation of the high-level language statements they're choosing
## ARM
- As of 2005, **98%** of mobile phones sold each year use ARM processors 
- As of 2009, ARM processors accounted for approx. **90%** of all embedded 32-bit RISC processors
### Example:
![[Pasted image 20241005135429.png]]
- The A6 processor on the iPhone 5 was the first apple SoC to use a custom design, based on the **ARMv7** instruction set
- The A8 processor in the iPhone 6 was the first 64-bit ARm based SoC. 