# Automatic Reference Counting

---

**Note:** This article talks a lot about [pointers](../Memory/bunchesofbytes.md) and [memory management.](../Memory/stacksandheaps.md) If you are unfamiliar, go read the related articles.

---

**The Problem**

Suppose we are trying to manage memory in our application, but we have some subset of the following constraints and issues:
  * The code must be correct and bug-free
  * Memory management is too complex to be done explicitly without risking safety
  * Our application is real-time or otherwise must consistently perform tasks within strict time constraints
  * Intermittent pauses are undesireable, regardless of how short or infrequent
  * We have a small, limited supply of memory
  * Our application has low-level, pointer-sensitive code

The first two constraints mean that we will need some kind of implicit memory management.

The next two constraints make [garbage collection passes](GarbageCollection.md) undesireable.

The last two constraints make many garbage collection techniques (especially those based on Stop and Copy) very undesireable, as they use tremendous amounts of memory and move data around frequently.

These constraints are common in cases where high-performance code is important (games, scientific computing, SaaS), in real-time or real-world applications (automotive, medical, etc.), or where hardware is limited (mobile/embedded devices).

Luckily ARC, or Automatic Reference Counting, works well in these cases.

---

**How ARC Works**

The fundamental idea behind ARC is to attach an extra value to each object to keep track of how many objects are currently referencing it. This value is called a *Reference Count.* For example, if **A** and **B** both contain pointers that reference **C**, then the *Reference Count* of **C** is 2 (assuming no other objects point to it). If we add another object **D** that also points to **C**, we must update the *Reference Count* of **C** to increment it by 1. If we free **A**, **B**, and **D**, then the *Reference Count* of **C** drops to 0, and we then know we can free it.

If the object currently is referenced by stack-allocated data, this also counts toward the *Reference Count*. Such references of course are removed when the associated function returns.

---

**Advantages of ARC**

* ARC uses less memory than most garbage collection techniques

* Objects are freed as soon as the last reference to them is removed, rather than during large garbage collection passes

* ARC is very simple to implement into a language, as it only requires a few extra bytes on each object and a few extra instructions added to each reference operation

---

**Disadvantages of ARC**

* Cyclical data structures can create problems. Suppose we have two objects, **A** and **B**, and each references the other. This creates problems because when all external references are removed, both will still have an active reference (that being the other object). Any cyclical structure will be unable to be freed.

* In multithreaded applications, atomic operations must be used to keep reference counts safely accurate. Unfortunately, atomic reads and writes can quickly become an expensive bottleneck. Atomic operations typically have similar to equal latency to [L3 cache operations](../Memory/caches.md). As this can be slow (40+ cycles), and subsquent branches can be dependent on the results (as in a branch to decide whether or not to free the object), this can dramatically slow the application. As a result, compilers for languages with ARC typically perform optimizations to remove unnecessary or redundant atomic operations, though this can be expensive and difficult, and still often misses common cases.

* Even outside multithreaded applications the additional work added to each reference operation can add up, as reference operations are typically very common due to their efficiency.
