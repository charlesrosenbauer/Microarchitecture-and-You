# The Problem of Communication and Scalability

---

When scaling and improving a system, there's always a bottleneck. Even when you remove the current bottleneck, another takes its place. If there were no such bottlenecks, our code would run infinitely fast. That clearly doesn't happen though.

While the exact bottlenecks faced will vary greatly from program to program, it appears as though the largest ones that will affect the majority of future software will be *communication*.

---

**Power Constraints**

Suppose we compare the electrical cost of two CPU operations; a 64-bit floating point operation, and a call to RAM. Which uses less power?

For those aware of what each entails, the floating point operation probably sounds like it uses more power; after all, it consists of a variety of high-precision arithmetic operations combined into one. However, it's actually the RAM request that uses more. Assuming the RAM request [misses at every level of the cache](../Memory/caches.md), the RAM request likely uses somewhere between *30-100 times more power* than the floating point instruction.

The reason is quite simple if you think about it; sending a signal over longer distances will use more power. A floating point operation, while complex, will mostly be confined to a single FPU on a single core, meaning the signals aren't traveling very far; just through a lot of components. It's also done in only a single clock cycle, or maybe a couple if the CPU is heavily pipelined. [Meanwhile, the RAM request will be completed over hundreds of clock cycles](../Memory/ram.md).

---

**Latency Constraints**

Clearly latency can be a bottleneck. If we need a value from RAM to perform the next CPU instruction, but we need to wait 300 clock cycles to get it, that's obviously something keeping us from getting the result faster.

This can be generalized to any form of information; if we have two threads and we need to send a message from one to the other, the message won't appear instantaneously. It takes time, and that ammount of time limits the kinds of problems we can solve in parallel.

Getting multiple cores to work together on a problem is much like getting multiple people to work together to solve a problem. Some problems might work fine with relatively little communication, but others won't. As a result, the kinds of problems we can efficiently solve are very strictly limited by the effectiveness of our communication. A program that heavily relies on frequent, detailed messages between threads may not run well if said messages must be sent across a large network. Analogously, coordinating a massive open-source project via smoke signals might be just a little bit difficult.
