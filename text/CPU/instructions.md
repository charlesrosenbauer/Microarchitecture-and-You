# CPU Instructions and Registers

---

Fundamentally, modern CPUs work by reading a linear stream of instructions. But how does this work?

Note: In this article, I will specifically be talking about how this works in *Register Machines*, as they are the dominant paradigm in modern hardware. However, it's important to note that other systems such as *Stack Machines* and *Belt Machines* also exist, and work a bit differently.

In this article we will be designing a toy instruction set to demonstrate what is necessary to create a functional computer.

---

**Constraints of Registers**

*Register Machines* work by applying operations on small pieces of memory called *Registers*. These *Registers* typically only store a single value (or multiple in some [SIMD](simdvmimd.md) architectures). *Registers* are used mostly to store short-lived variables that don't need to be stored in main memory.

The CPU operates on these registers by reading through a sequential stream of instructions. This instruction stream may branch in many places, and the CPU must, based on the state of the program, decide which way to go. Each instruction typically provides the CPU with a single, simple operation to be applied to a group of specific registers. For example, an instruction like ```add R0 R1 R2``` may tell the CPU to add the contents of the R1 and R2 *registers*, and store the result in the *R0* register.

This code (```add R0 R1 R2```) is an example of *assembly*, one of the lowest-level forms of programming. *Assembly* is a human-readable language where each line of code maps directly and unambiguously to an actual instruction going into the CPU. Of course, "readable" is a relative term; assembly can be extremely difficult to read (especially when it's heavily optimized), though it is much easier to read than straight *machine code*, the individual byte values that the CPU is *actually* reading.

It's important to note that *registers* are not actually parts of main memory; they do not exist as part of RAM, or anything [caches](../Memory/caches.md) can map to. By extension, they have [no memory addresses](../Memory/ram.md). They exist purely inside of each CPU core, and nowhere beyond. The only way values can be read from or written to them is through instructions specifically picking them. In addition, there is no way for instructions to dynamically decide which *registers* to use; you can't have something like ```add R0 R1 RX``` where ```X``` is some variable. If you want to do these kinds of dynamic operations, you must do so on RAM, not on *registers*.



---
