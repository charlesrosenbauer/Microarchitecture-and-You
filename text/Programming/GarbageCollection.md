#Garbage collection

---

**What is Garbage Collection?**

Garbage collection is a technique used for managing memory in the [heap](../Memory/stacksandheaps.md). While a variety of data structures exist to manage memory very efficiently, they often impose limitations that can make cleanup of that memory difficult unless very specific conditions are met. As a result, heap allocation is necessary for most nontrivial applications. Unfortunately, heap allocation comes with many responsibilities; all objects must be explicitly freed when no longer needed, and fragmentation must be managed to reduce the total amount of memory required.

Garbage collection is simply the idea that the application can handle this all for you; there are many techniques for doing this, though most follow the following pattern:
* Allocate data into a special space
* When the space fills up, iterate through all items in memory, checking each to verify that it can currently be accessed from the call stack and CPU registers
* Free any objects that no longer are visible, and resume normal operation

---

**Why GC Specifically?**

There are other alternatives. ARC, manual memory management, and substructural type systems can all solve this problem to varying degrees, though each has its own advantages and disadvantages.

The main advantage of garbage collection is that it requires little to no concern from the programmer's perspective (except in some minor edge cases). Another advantage is that some types of garbage collectors are able to compact memory, eliminating fragmentation and (in some cases) improving [cache-friendliness](../Memory/caches.md).

The main disadvantages of GC are that it can create frequent and unpredictable pauses in the application, that it doesn't always scale well across multiple cores, and that it is less efficient than other techniques. The unpredictable pauses and lower efficiency have traditionally been the biggest disadvantages (after all, do you want the computer in your car to stop responding for a GC pause?), though the poor parallelism scalability may eventually become an even larger issue.

---

**Mark and Sweep**

---

**Stop and Copy**

---

**Generational Garbage Collectors**

---

**Conservative Garbage Collection**

---

**Edge Cases of GC**

---
