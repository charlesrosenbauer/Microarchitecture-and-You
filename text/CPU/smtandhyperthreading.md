# SMT and Hyperthreading

---

**What's the Difference Between the Two?**

*SMT* is *Simultaneous MultiThreading*.

*Hyperthreading* is exactly the same technology as *SMT*. The only difference is marketing; *Hyperthreading* is Intel's trademarked buzzword for *SMT*.

From here on, I will refer to both as *SMT*.

---

**What is SMT?**

In modern CPUs, there can often be cases where the CPU *stalls*. These *stalls* are cases where the CPU does not have any work that it can currently perform, normally because all future work is dependent on some data that is not currently available.

This data can be unavailable due to values still being in the pipeline, or because they [haven't been loaded from RAM or caches yet](../Memory/whycaches.md).

In the case of pipeline dependencies, *stalls* may only be for a couple clock cycles. In the case of memory operations, there may be dozens to hundreds of cycles of *stalls*.

Fundamentally, *SMT* works by having each CPU core keep track of multiple program threads. When one thread *stalls*, the CPU can switch to the other thread. This way, the time that would normally be wasted waiting on data can be spent working on another thread.

---

**Disadvantages of SMT**

The main disadvantages of *SMT* are cache pollution and chip complexity.

*Cache Pollution*: Caches only have [limited space and associativity](../Memory/caches.md). If there are multiple threads sharing a cache, then switching to one thread will likely load data into the cache that not be necessary to the operation of the other, likely ejecting many other useful data from the cache in the process. This fighting over the cache can lower performance in some cases.

*Chip Complexity*: Keeping track of multiple threads simultaneously, and which data pertains to which thread can dramatically increase the complexity of the processor core. If threads are allowed to be from different programs, caches may become more complex, as they have to keep track of which cache lines are owned by which program. For this reason, outside of *HPC*, it is rare for CPUs to support more than two-way SMT (two threads per core). Some more high-end CPUs can support more than this however. Many supercomputer and mainframe CPUs support up to 4 or even 8 -way *SMT*. This is normally used to cover very long memory *stalls*, such as from RAM, or as is often the case in mainframe systems, disk-dependent database lookups.

---

**When Does SMT Help?**

How much *SMT* helps depends very heavily on the software. If your software gets along nicely with the [caches](../Memory/caches.md) and the [prefetcher](../Memory/prefetch.md), for example if you make heavy utilization of linked lists and binary trees, then it's unlikely that you'll see a ton of benefit from it, as your program will likely have few stalls.

The other common case is harder to determine and optimize for. *ILP* or [Instruction-Level-Parallelism](ipcandilp.md) also plays a role. If you have some code with relatively few dependencies between individual instructions, *Out-of-Order Superscalar* CPUs (the primary class of microarchitecture used in high-end CPUs) will be able to execute more instructions per cycle. If the CPU can find 2 instructions within a short distance that can all be run at the same time, it will do just that. If it finds 3 instructions where each is dependent on the last, it cannot run them in parallel.

Such CPUs can often run 2-8 instructions in parallel, so if they can't find that many in your code, they may utilize *SMT* to look for some in other threads. Once again, this is generally pretty hard to measure, though if your code is doing something that could theoretically be run in parallel, chances are the CPU has its hands full and *SMT* won't bring much benefit.

---

**Misconceptions**

The most common misconception that *SMT* commonly has relates to performance. Commonly, an *SMT*-enabled CPU may have twice as many advertised threads as cores. This often gives the impression of higher performance; after all, twice as many threads means twice the performance, *right?* As explained above, this isn't quite the case. The numbers can vary wildly from application to application, but normally only end up with one thread picking up the slack of another.
