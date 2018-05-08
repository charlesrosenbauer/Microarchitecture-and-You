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

*Chip Complexity*: Keeping track of multiple threads simultaneously, and which data pertains to which thread can dramatically increase the complexity of the processor core. If threads are allowed to be from different programs, caches may become more complex, as they have to keep track of which cache lines are owned by which program.
