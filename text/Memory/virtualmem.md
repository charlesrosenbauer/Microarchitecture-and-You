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

**Memory Paging**

Normally, RAM can be thought of as a [giant, flat, one-dimensional array of bytes](bunchesofbytes.md). Memory paging allows an extra layer of indirection. This helps mitigate issues due to memory fragementation; cases where there may be enough memory to store all the data needed, but no individual block of free space big enough for it.

Think of memory fragementation a bit like trying to pack things into a small space; the arrangement of things already in the space has a big impact on whether or not something new can fit. It may be that the only way to fit a new item would be to either break it up into smaller parts, or move things around to make room. These solutions pose problems in computer memory however.

First off, the arrangement of bytes in a data structure is very important. It can have a huge impact on how quickly data can be located, which has a huge impact on performance [given high RAM latencies](whycaches.md). Plus, if we break up our data, it either must be invisible to the program, or it must be something easy to work around in software. If either of these conditions are not met, it will pose significant problems.

Secondly, moving things around in memory is expensive and difficult. If we move things in a way that changes memory addresses, we wind up with invalid pointers, resulting in a large number of errors as our data is no longer where we expect to find it. Even if we can do this, copying and deleting values in RAM, especially large quantities of memory is slow and uses extensive amounts of power.

The solution is memory paging.

Simply put, we break up memory into standard-sized chunks. Common sizes are 4kB and 2MB, depending on the OS and hardware, though this can vary quite a lot. Each chunk, called a page, is simply a range of bytes in RAM. These can be given to different programs with different permissions.

---

**Address Translation and the TLB**

[To be Written]

---

**Disadvantages**

[To be Written]

---

**Performance Considerations**

[To be Written]
