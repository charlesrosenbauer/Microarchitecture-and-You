# Instruction and Data Caches

---

**On Instruction and Data Caches**

Most modern CPUs have two different types of [L1 caches](caches.md); the *Instruction Cache* and the *Data Cache.*

The *Data Cache* is just your typical, [coherent](cachecoherency.md) cache for whatever data the CPU core is currently using. Being L1, it's also pretty small (=< 64kB) and fast (1-3 cycle latency).

The *Instruction Cache* on the other hand is quite different; it is used exclusively for data that the CPU is executing (or that it expects to execute soon) and often does not maintain any form of cache coherency, as your code can normally be assumed to not be changing.

---

**Performance Issues of Instruction Caches**

One big problem with *Instruction Caches* is that the CPU can perform much better with a much larger cache, however the maximum latency of the cache has a large impact on the length of the pipeline. The CPU pipeline's *Fetch* stage will be roughly equal in cycles to the maximum latency of your *L1 Instruction Cache*, meaning that making the cache larger either requires that the CPU's clock speed decrease (not ideal) or more pipeline stages be added (making branch prediction accuracy *much* more important, also not ideal). However, if the cache is too small, the CPU will frequently be unable to find the instructions it needs within the cache, causing unavoidable CPU stalling. Out-of-Order Execution can't even save you here, as without new instructions, it can't find other work to do!

Another concern is [*Prefetching*](prefetch.md). CPUs will often utilize their branch predictor to predict what data to load into the *Instruction Cache*, many cycles in advance. As branches are quite frequent (every 4-8 instructions in most code), and as wide-issue CPUs load multiple instructions per cycle, even a highly accurate branch predictor will mispredict every few dozen cycles, and having to load missing data from farther than a nearby cache can be extremely expensive. Some CPUs will even in some cases prefetch both outcomes of a branch to avoid this kind of situation.

---
