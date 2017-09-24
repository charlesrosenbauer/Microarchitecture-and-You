# Major Bottlenecks

---


Here I'll go through several bottlenecks, and what kinds of code can cause them. It may not be easy to determine for sure which kind of bottleneck you're facing, but there may be some tell-tale clues. In some cases, a slight algorithmic change may be good enough to solve the problem, but

---

**Memory Bandwidth**

Memory bandwidth may not seem like a terribly big bottleneck at first; modern systems can often move tens of GB in and out of the CPU every second. If you have a 3 GHz single-core CPU with 2 GHz RAM and a 64b RAM bus, you can theoretically load a value from RAM every 1.5 clock cycles, though it may be much lower in practice due to CPU stalls due to RAM latency, and due to the fact that CPUs will typically load entire [cache lines](../Memory/caches.md) to and from RAM at a time.

This may not sound like a big deal, especially when you consider that x86 CPU cores will typically make a RAM request every 6 clock cycles or so, the vast majority of which will likely hit the cache. However, if your program accesses large amounts of memory in a random fashion (i.e, frequently searching through large data structures with low temporal locality), or if your program frequently loads large data sets in and out of RAM (large matrix operations, map/reduce/etc. operations on large arrays, etc.), the program will likely wind up with frequent cache misses and high memory usage.

For the memory bandwidth needs, it's also important to consider how much work needs to be done between loads. If you have an array of integers and you need to square each element of the array, the CPU can square the numbers very quickly, and so will need a new value almost immediately. In contrast, if you have an array of objects and are applying a function to each one that involves a loop or two and some other non-trivial problems, then there will be much more time between RAM requests and the bandwidth requirements will go down. Note however that if said object is very large, then it may still result in high bandwidth requirements.

Ultimately, if you want to get some idea of the relative bandwidth requirements of some code, ask yourself three questions:
1. How likely is it that this data is already in the cache? If you've accessed it recently, and you haven't used a ton of other data since then, there's a chance it might still be in a cache. If it's in a cache, you don't have to worry about external bandwidth.
2. What do you think the approximate data-to-work ratio is? As in, are you doing a lot of work on a relatively small amount of data, or a small amount of work on a lot of data? The first case is much better for memory bandwidth.
3. How big is the data set you're working with compared to the CPU cache? If you don't know how big your CPU cache is, it would be very good to know. If you can't figure it out or you're developing an application for a wide range of systems, then you can typically assume that PCs and laptops will have 2-16 MB and phones will have a couple hundred KB. If the data set you're working with is bigger than the cache, you can expect a lot more cache misses than if it were smaller, especially if you are frequently applying map/reduce/etc. operations across the full dataset.

If your program utilizes multiple cores however, then I have some bad news. All of your cores share the same amount of bandwidth. If you have one core that's using a lot, and the others are using very little, you might be fine. If you have multiple cores at a time using a lot of bandwidth, then chances are you'll have a bottleneck; your CPU just won't be able to move all the data it needs in and out of RAM.

*What can I do about this?*

Try to minimize your bandwith. If that's not an option, consider how friendly your code would be to [SIMD](../CPU/simdvmimd.md). If it would be suitable, you may actually be better off running your code on a GPU, as they have much higher bandwith than CPUs, sometimes getting close to a TB/s of bandwith. GPUs might be less appropriate for situations that need very large datasets however, as even the high end ones may only have access to a fraction as much RAM as the rest of the system.

It may also be that the way you're accessing memory isn't very efficient. Having a basic understanding of Cache-Oblivious Algorithms may help reduce your memory usage in these kinds of situations.

It might also be important to note that RAM transactions work both ways; excessive writing to RAM can also impact bandwith in a similar way.

---

**Memory Latency**




---

**Clock Speed**


---

**Core Count**
