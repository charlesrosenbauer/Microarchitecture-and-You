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

*Mark and Sweep* is a very common type of garbage collection. It consists of two passes; *Mark* and *Sweep*.

In the *Mark* phase, the garbage collector pauses the application and then iterates through every pointer in the call stack and in registers. For each pointer, it loads the object pointed to and "marks" it (every object in the applicaiton is given a special bit or byte invisible to the programmer specifically for this purpose). After marking the object, it checks to see if the object contains any pointers to other objects. If it does, the garbage collector jumps to those objects as well and marks them if they have not been marked already. If an object has already been marked, it does not need to marked again, nor do the objects it references. At the end, the garbage collector has iterated through all data structures visible from the call stack, and has marked all objects that are visible to it.

In the *Sweep* phase, the garbage collector begins to iterate through all objects in memory. As any object that is unmarked is not accessible from the call stack, the object is deemed to be no longer needed, and is promptly freed by the garbage collector. When freed, the memory used by the object is simply marked as available, and future objects can be allocated in their place.

One advantage of *Mark and Sweep* is that it does not move objects around. This may make allocation a bit slower in some cases (as the application must locate an available space when objects can be organized pretty randomly), but it means that languages where pointer arithmetic is possible (C, C++, Go, etc.) may be compatible with this technique without risking errors.

One disadvantage is that *Mark and Sweep* normally is implemented in a way that it only does garbage collection if there is no space left for new objects. This means that any data structures needed by the garbage collector to iterate through memory cannot allocate new memory. This can make the *Mark* phase more complex and require techniques like *Pointer Reversal*.

---

**Stop and Copy**

*Stop and Copy* is a garbage collection technique that splits memory into two different regions; the *Old Space* and the *New Space*.

The *Old Space* is used for allocating new objects. Allocation works like a stack where newly allocated objects are added to the top until the region fills up.

The *New Space* on the other hand is mostly left empty until it is needed.

When the *Old Space* fills up, the garbage collector pauses the application and begins copying objects from the *Old Space* to the *New Space*. Specifically, it only copies objects visible from the call stack and registers. This process is very similar to the *Mark phase* of *Mark and Sweep*. The difference is that instead of simply marking the object, the object is copied into the *New Space*, then the old copy is marked as copied. Somewhere in the old copy a *forwarding pointer* is stored; a pointer describing where the new copy is located. If we copy an object **A** that stores a pointer to an object **B** that has already been copied, we must use the forwarding pointer of **B** to update the pointer in the new copy of **A** such that it references the new copy of **B** rather than the old one.

After all objects are copied from the *Old Space* to the *New Space*, the roles of the two regions is swapped; the *Old Space* becomes the *New Space* and the *New Space* becomes the *Old Space*.

The biggest advatage of *Stop and Copy* is that it is pretty fast; allocations are incredibly cheap (they're just stack allocation) and while objects are frequently copied and moved around, only the live objects are copied; the rest are simply left in the *Old Space* and forgotten. *Stop and Copy* also eliminates any fragmentation, as stack-like allocation is always fragmentation-free.

The disadvantages of *Stop and Copy* are that it does excessive copying of data (which can be expensive if you have a lot of live objects or frequently fill up the *Old Space*) and that it uses much more memory than other techniques. *Stop and Copy* also suffers from the same problem as *Mark and Sweep* in regards to having to use *Pointer Reversal*.

---

**Generational Garbage Collectors**

*Generational Garbage Collection* is a varation of *Stop and Copy* that reduces the number of times objects need to be copied by using additional regions and a heuristic.

This technique is based around the fact that objects that survive multiple GC passes are likely to survive more. This is modelled by keeping track of how many GC passes each object has survived, and moving objects to special regions after they have passed a certain threshold. Passes on these regions are run less often, as a long-lived object is less likely to be removed soon, so there is less of a reason to check it.

*Generational Garbage Collection* is used in many high-performance languages such as Java and C#.

---

**Variations on GC**

A few varations on the above algorithms exist.

In order to minimize the length of each GC pass, some garbage collectors are designed to be able to be split into multiple small passes. These passes clean up some but not all of the garbage, and as a result are much faster, but must be run more often.

As the garbage collector is moving data around, parallelism can be unsafe when the collector is running. When a collector is running, work on all other threads is halted to prevent the collector and the application from interfering with each other. It's also important to note that multiple GC passes running in parallel can suffer from similar problems, so often times GC passes are either exclusively single-threaded, or alternate between single-threaded GC passes and parallel but restricted passes. The advantage of this is that the collector normally runs fast and sloppy, and then occasionally comes in with a more precise single-threaded collector to clean up anything the parallel passes miss.

The limitations of parallel garbage collection may become an issue in future software.

---

**Conservative Garbage Collection**

---

**Edge Cases of GC**

---
