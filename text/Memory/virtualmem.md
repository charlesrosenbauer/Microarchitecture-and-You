# Virtual Memory

---

**What is Virtual Memory Addressing?**

Virtual Memory Addressing is the technique of hiding memory fragementation by providing each application with its own private address space. This is most often done via a combination of memory paging and address translation.

---

**What Problems Does Virtual Memory Solving?**

Virtual memory solves a few different problems:
  * It hides fragementation of memory from programs
  * It adds an additional layer of separation between programs, which is useful for security
  * It allows the Operating System to move memory to places outside RAM (like the disk) when there is not enough available

It also solved some problems in the past that are no longer necessary, mainly that it allowed multiple applications to share an address space larger than what any individual application could address. This was important in many 32-bit machines, though it unnecessary in 64-bit machines at the moment.

---

**Fragmentation**

Normally, RAM can be thought of as a [giant, flat, one-dimensional array of bytes](bunchesofbytes.md). Memory paging allows an extra layer of indirection. This helps mitigate issues due to memory fragementation; cases where there may be enough memory to store all the data needed, but no individual block of free space big enough for it.

Think of memory fragementation a bit like trying to pack things into a small space; the arrangement of things already in the space has a big impact on whether or not something new can fit. It may be that the only way to fit a new item would be to either break it up into smaller parts, or move things around to make room. These solutions pose problems in computer memory however.

First off, the arrangement of bytes in a data structure is very important. It can have a huge impact on how quickly data can be located, which has a huge impact on performance [given high RAM latencies](whycaches.md). Plus, if we break up our data, it either must be invisible to the program, or it must be something easy to work around in software. If either of these conditions are not met, it will pose significant problems.

Secondly, moving things around in memory is expensive and difficult. If we move things in a way that changes memory addresses, we wind up with invalid pointers, resulting in a large number of errors as our data is no longer where we expect to find it. Even if we can do this, copying and deleting values in RAM, especially large quantities of memory is slow and uses extensive amounts of power.

The solution is memory paging and address translation.

Simply put, we break up memory into standard-sized chunks. Common sizes are 4kB and 2MB, depending on the OS and hardware, though this can vary quite a lot. Each chunk, called a page, is simply a range of bytes in RAM. However, for each page there are two ranges of addresses; a physical address range, and a virtual address range. The physical range is the range of addresses for the memory according to RAM, and the virtual range is the range of addresses according to the application. These are almost always different addresses and require translation.

---

**Address Translation**

Translation is typically pretty easy. For example, if we have 4kB pages, each page will also have 4kB alignment. Consequentially, the last 12 bits can be unchanged in translation, and everything before that can simply be replaced via a table lookup. Every time we double the size of the pages, the number of bits at the end of the address that stay the same is increased by one.

---

**The TLB**

Different applications will be working in different address spaces. According to each program, memory starts at 0 and goes off to some higher address (depending on how much memory the application is using). Of course, this means that two applications can both request the same virtual address while referring completely different physical addresses. It also means that in multi-core processors with [coherent caches](cachecoherency.md), we run into some pretty significant problems.

First off, what happens if we have two different applications, each running on a different core (or a different thread on a single core with SMT). However, these two cores share a cache in common, even if it's an L2 or L3 cache. Suppose each application then requests the value at address 16. Because these applications have different virutal address spaces, they are of course referring to different locations in memory. But how do we store that in the cache?

If we try to store each [cache line](caches.md) with the same address (16), we need some way to differentiate them, or else the cache will treat them as the same data (creating huge bugs and security risks). We could add an additional couple bits to each address to store which application is using it, though this makes it difficult to have any kind of memory shared between applications (which can be useful in some cases). The other solution, which is what is most commonly used, is to store the physical address of the memory, and do the translations from virtual to physical addressing before using the cache at all.

The piece of hardware used for this is called the Table Lookaside Buffer, or TLB. The TLB knows where to look for the virtual memory tables in RAM that the OS uses for your application, and stores a small cache of entries that it can search for to quickly translate your address before searching through the cache. These TLB caches are quite small and are often multi-leveled (for example, AMD's Zen architecture uses a 64-entry L1 TLB and 1536-entry L2 TLB. Note that L1 and L2 here refer to levels of the TLB cache).

Each TLB cache entry may represent several thousand to million addresses, but jumping around the address space too much can easily result in TLB misses. If there's a miss in the L1 TLB, it tries the L2 TLB. If it misses there, chances are it will require a RAM access, causing a stall of ~300 cycles. This will normally only happen often in applications using a large amount of memory (and thus pages).

TLB entries typically consist of several fields:
  * Which application the page applies to
  * The size of the page
  * The first X bits of the virtual addresses (note translation details above)
  * The first X bits of the physical addresses
  * The read/write permissions for the application

---

**Permissions**

Different memory pages have different permissions. For example, the OS can read any application's memory, but applcations can only read each other's memory if allowed. For example, dynamically linked libraries are often loaded into memory in one location, but allow multiple applications to read them. However, to prevent programs from interfering with each other, the code in the libraries are write-protected, allowing only the OS to make modifications.

---

**Disadvantages**

There are three major disadvantages to virtual memory.

First off, the TLB increases power consumption and cache latency quite substantially. Since every memory transaction requires a TLB lookup to translate it from virtual to physical addressing, this adds up to a lot of power. It's even more power due to the fact that it needs to be performed before loading a value from the L1 cache (which needs to be very fast as well). It can be sped up by making it smaller (making it more likely to miss, and adding more complexity for added cache levels), or by increasing voltage, [which is awful for power efficiency](../Misc/clockvcores.md).

Secondly, virtual memory makes inter-process communication harder. If two programs need to share data, there is now a high probability that they will be using completely different addresses for the data, making it almost impossible to use pointers (with an exception for relative pointers).

Third, any calls to the operating system or drivers involving pointers requires that the OS manually translate between address spaces. This is possible as the OS has access to this data, but it is slow and would be unnecessary if all applications shared the same address space.

A solution to these problems would be to do away with virutal memory, but not permissions. A Permissions Table would still need to be kept for each application, however could be done in parallel with the cache lookup, dramatically easing latency requirements. In this case, the cache lookup would be performed, but discarded if any permission conflicts are detected. As no translation would be necessary, this would result in a Permissions cache much smaller than a typical TLB, resutling in even better performance. It would eliminate the need for translation during OS and driver calls, and would make pointers in shared memory much simpler. The disadvantages of this alternative would be backwards-compatibility conflicts, and that it might require some work-arounds for running virutal operating systems.

---

**Performance Considerations**

In applications with a large amount of memory, it's best to avoid frequent random accesses across a space larger than the size of a typical page table. For example, if you have 4kB page tables, any application larger than about 6.5 MB will likely be unable to store the entire address space in the TLB caches. This means that accessing memory outside of the most recently used pages will likely result in TLB cache misses in addition to misses in the main caches. This doubles memory latency.

Linux systems almost always use larger page sizes (2 MB typically), meaning that on Linux, this problem shouldn't normally occur for any application using less than 3.3 GB.

This mostly applies to applications where random RAM accesses over large areas of memory are frequent (for example, searching through a large database stored in RAM).

The best way to avoid this is to [increase spatial and temporal locality if possible](caches.md). In other words, after using data in memory, try to use [data likely stored nearby in memory](bunchesofbytes.md) as much as possible before moving onto something further away.
