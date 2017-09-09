# The Problem of Cache Coherency

---

**The Basic Problem**

Suppose we have a system consisting of two cores, each with a local cache, and some RAM. In RAM, some variable exists that currently is equal to 4.


Next, each core loads the variable into their respective caches. The first core sets it to 5, and the second core sets it to 7.

A few questions arise:
  * What value does this variable have?
  * What value should be sent back to RAM?
  * If each core attempts to read the value of the variable, what value should they get?

This problem, called *Cache Coherency,* arises whenever data that can exist in multiple caches simultaneously is capable of being modified. This issue alone creates many immense scaling problems in multicore processors.

There are many potential solutions to this, which will be covered in the remainder of this article.

---

**Common Solutions**

Typically, Cache Coherency is solved by having some way for all the caches to decide on a single global value that must be correct in all caches. This is normally decided based on which version is most recent, though the exact order is often difficult or impractical to get correct. Of course, the exact order of asynchronous memory accesses typically doesn't matter too much anyway, at least not over very short time scales.

The two most common solutions are Write Updates and Write Invalidation.

*Write Updation* : Every time a cache line is modified, the cache containing it broadcasts the changes to every other cache that could conceivably contain a copy.

*Write Invalidation* : Two types of caches exist; read-only, and read-and-write. Read-only caches tend to be local and specific to individual cores. Read-and-Write caches tend to be larger and shared between all cores. This shared cache isn't strictly necessary, as RAM can serve the same purpose. When a cache line is modified in a read-only cache, the results are written back to the shared cache / RAM. At the same time, the address of the cache line is broadcasted to every other cache, which invalidates all other copies of the cache line, requiring that all cores get the updated value from the shared cache / RAM.

These are both discussed in more detail further below, as well as some other solutions.

---

**Write Updation**

Theoretically, this should provide better performance than Write Invalidation, as cache invalidation sends results to a more distant cache, increasing average memory latency, especially if said cache line is frequently read by other cores.

It does however come at additional costs in terms of power consumption, as all of the details of any writes must be broadcast to all other cores. Global broadcasting of data scales very poorly (N^2). While Write Invalidation still performs such broadcasting, it can use less power by sending less data (only the cache line address instead of both the memory address and the data). Cache line invalidation should also occur less frequently, as if the cache line is frequently modified, it will spend most of its time in the shared cache / RAM, where only one valid copy exists and thus coherency is intrinsically maintained.

Write Updation is common in CPUs, though many CPUs are starting to switch to Write Invalidation instead.

---

**Write Invalidation**

Write Invalidation scales better than Write Updation; it still requires N^2 broadcasting, however these are much less common, as explained above. However, there are still problems with it.

The main problem with Write Invalidation arises during the time between when an invalidation signal is sent, and when it is received. Of course the signal is not instantaneous, and the amount of time it takes depends on how distant the cores are, which increases as core counts increase.

If a core writes a value to a cache line after the line is invalidated, but before the invalidation signal is received, what happens to said value? If the value is lost, this can create a race condition, especially if the written value is in the invalidated cache line, but was not itself modified. This problem requires a slightly more advanced coherency protocol to be implemented in the hardware.

It is also important to note that if a cache line exists in only one cache (as in, only one core is using it currently), then no cache invalidation is necessary. Keeping such data in a local cache, even if it is being modified is a good way to improve performance. This does require some extra complexity however, and GPUs will typically not use this optimization.

Due to the improved efficiency and scalability, Write Invalidation is the primary coherency technique used in GPUs, as well as many recent CPUs.

---

**The Fundamental Problem: Shared Mutable State**

Fundamentally, coherency comes down to mutable state. If there is no data being modified, there is no need to manage asynchronous changes to that data. Of course, software depends heavily on data being modified, so that's not something we can avoid.

We can go further than that though; we can say that [*Communication* is Fundamentally the biggest factor in scalability](../System/scalability.md). This makes sense; a program with tons of parallelism, but no necessary communication between threads is about as scalable as you can get. Embarassingly parallel problems, or in other words anything that can be expressed as a *map* operation, scale very well with parallelism. However, as soon as you add in more communication, such as with a *parallel reduce* or *scan* operation, suddenly organized communication becomes necessary, slowing things down.

Due to the way that memory works in modern systems, such communication is almost exclusively done via shared mutable state in memory. The same kind that necessitates cache coherency.

---

**Scratchpad Memory**

One solution is Scratchpad memory. This, in principle, is quite simple; rather than do all the communication in the distant RAM, you bring the RAM closeby and work there.

Scratchpad memory consists of typically a few dozen to a couple hundred KB of [SRAM](commonmemory.md) built into the CPU, often shared by multiple cores (though variations with strictly local access exist too). Scratchpad is directly addressable, meaning that unlike a cache, it *does not* represent a different part of memory. It functions like RAM, but just faster.

There are limitations to Scratchpad memory though. It often adds extra burdens to programmers/compilers, can reduce portability in cases where software is reliant on it, and adds extra work for the Operating System, as it has to be copied somewhere during a context switch.

Most CPUs (aside from some embedded CPUs) don't use Scratchpad. GPUs and the Cell CPU (from the Playstation 3) do contain scratchpad however. In the case of GPUs, several dozen cores might all be able to access the scratchpad simultaneously, with a latency of only a clock cycle or two, allowing for very rapid thread communication.

---
