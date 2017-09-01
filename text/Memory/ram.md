# RAM

---

**What is RAM?**

RAM, or Random Access Memory, is your computer's temporary memory. The computer uses this to store data that it is currently using for relatively fast access. Typical bandwidth between a CPU and RAM is currently around 20-60 GB/s. [Latency tends to be around 200-300 clock cycles](whycaches.md), with this being worse when the CPU operates at higher clock speeds. There are also other cases where it can be worse, mentioned below.

---

**How RAM works**

Here I'll mostly be discussing [DRAM](commonmemory.md), or Dynamic Random Access Memory. More specifically, DDR SDRAM, which is the most common type in consumer computers.

RAM is divided into banks, or blocks of memory that can be read from / written to in parallel. Multiple reads/writes to the same bank must be performed sequentially. However, most computers have dozens to hundreds of banks and can only take a couple in parallel at a time anyway, making conflicts relatively uncommon. Added to the fact that the vast majority of the time the CPU will be mostly working directly with data in the cache, and RAM banks tend not to be a major concern.

RAM also tends to be organized in *word lines*. Word lines are individual lines of bytes in RAM that are read simulatenously. Loading memory from word lines tends to add substantial latency (typically an extra couple dozen cycles. Check *CAS timings* on your RAM for details), the entire word line will be loaded to a specific location for faster access. Multiple subsequent reads/writes to the same word line will result in lower latency.

Note however that word lines typically aren't much larger than cache lines, if there is a difference in size at all. So in most cases this isn't something you need to worry about too much as the CPU will often be able to manage it for you.

---

**How Do I Use RAM effectively?**

* Write [cache-friendly](caches.md) and [prefetch-friendly](prefetch.md) code to avoid unnecessary RAM access.
* Avoid random jumping around RAM too much, as this can cause misses in the TLB cache.
* Remember that while RAM is *Random Access*, that doesn't mean that predictable memory access isn't substantially faster.
