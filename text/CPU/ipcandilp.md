# IPC and ILP

---

ILP (Instruction-level parallelism) and IPC (Instructions per cycle) are important factors in modern CPUs, though they are often somewhat misunderstood. So what exactly do they mean, what is the difference between the two, and why does it matter?

---

**Basis**

Most modern CPUs are often *Superscalar*. A *Superscalar* CPU is capable of issuing multiple instructions in parallel each clock cycle. ILP and IPC are subtly different descriptions of how a CPU issues parallel instructions.

---

**Definitions**

*ILP*, or *Instruction-level Parallelism*, is a measure of how many instructions can be run in parallel at a time in a particular piece of code.

A simple case of how this can work in practice is the following:

```
c = a + b
f = d - e
g = c + f
```

Note that **c** and **f** have no dependencies, allowing them to be calculated in parallel. Meanwhile, **g** depends on both **c** and **f**, meaning that it cannot be calculated until after **c** and **f**.

In this code, there are 3 instuctions, but the result can be calculated in only 2 cycles (assuming that each operation only takes 1 cycle). 3 instructions / 2 cycles = 1.5 instructions in parallel, or an ILP of 1.5.

Most applications will typically have an ILP in the range of 2-3, though some applications will be closer to 1 and others (especially those that do a lot of heavy mathematics) may be much higher.

--

*IPC*, or *Instructions per Cycle* is similar, but subtly different, as it is a metric for hardware, not software. The *IPC* of a CPU is a measure of the average *ILP* across all applications while running that CPU. Note that there is often a very big difference between *Theoretical IPC* and *Actual IPC*, as an extremely wide issue CPU may in theory be able to issue a large number of instructions in parallel, but most applications will likely be unable to utilize it all.

*IPC* also takes into account factors like how [cache misses](../Memory/caches.md), branch mispredicts, and the dozens of other architectural factors impact performance of applications in practice. Changes in these kinds of factors are the main ways in which CPU performance is improved today.

---

**Why it Matters**

Your CPU's clock speed isn't the only factor that impacts single-threaded performance. A CPU with a high clock speed may be outperformed by a slower CPU with a higher IPC. It's important to remember that there are a ton of factors here that are not immediately obvious.

The most visible details about a CPU may be its clock speed and core count, but those details alone tell you very little about it. A basic understanding of ILP and IPC like the one provided here can serve as an introduction to understanding the CPU, how it impacts performance, and how you can write code to better take advantage of your hardware.
