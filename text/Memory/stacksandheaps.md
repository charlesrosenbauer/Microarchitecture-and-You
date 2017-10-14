# Stacks and Heaps : Program-wide Memory Layout

---

**Notes:**
* For any mention of pointers, bytes, etc., the [Bunches of Bytes](bunchesofbytes.md) article covers this.

---

**Overall Layout of Memory**

Overall, programs are laid out typically with instructions starting shortly after address 0. 0 itself is inaccessible due to it being used for null values. Trying to read data with a 0 pointer, or any other inaccessible data for that matter, will trigger a type of error called a Segmentation Fault. The size of this space of course depends entirely on how big your executable file is. The next couple megabytes of memory afterward are the *call stack*, and everything after that is either part of the *heap*, or not accessible.

Of course, it may seem odd that all programs start memory near address 0; this appears as though it should create conflicts with multiple programs. This is solved with *virtual memory addressing*, though the specific details of virtual memory are beyond the scope of this current article. As for why this is done, it simplifies the programming model and helps keep programs from interfering with each other, either by accident or malice.

If a program requires more memory, it typically gets the memory by making a request to the operating system for an additional *memory page.* This is, once again, beyond the scope of this current article (it's part of the *virtual memory* system), but has a few consequences worth noting here:
  * When the program needs more memory, it has to make explicit calls to the OS for a new memory page. These pages typically range from 4kB - 2MB, though can be much bigger.
  * When a program has spare memory, it will have to explicitly give pages back to the OS. It however *has to guarantee* that the data inside said pages *will no longer be needed.*
  * Accessing pages that the program no longer has access to, or does not yet have access to, will cause segmentation faults.
  * The OS provides memory to programs in large, fixed-size chunks.

---

**Call Stacks**

Most modern software is based around the idea of a call stack. This is a stack (LIFO) used to quickly allocate data that will be used only for the duration of the current function scope, and is normally a couple megabytes in size. Every time a function is called, the *stack frame* for that function is pushed onto the top of the stack. When that function returns, the *stack frame* is popped off. For this reason, multithreaded programs will utilize multiple stacks, one for each thread. Some parallelism-focused languages even avoid using a call stack and choose to use a heap-allocated graph instead, as stacks don't really support a parallel form of stack frame.

As the stack is of fixed size, it is capable of running out of memory. This most commonly happens from having too deeply nested functions (i.e, from non-tail-optimized recursion) or allocating too much onto the stack. When the stack runs out of space, the program normally crashes due to a *Stack Overflow Error.*

The *stack frame* is the collection of all the data in a function that:
  * Cannot fit inside registers
  * Is both created and destroyed during the duration of the function's evaluation
  * Is necessary to return the function to its state before the function call
  * Is not better allocated on the heap

The last point might be a bit ambiguous. Most languages will avoid doing any kind of *dynamic allocation* to the stack, as in, allocations where the exact size of the data cannot be easily determined at compile time. Common examples include trees and strings. Many languages will also often prevent stack allocation of arrays, as large ones can easily exceed the size of the stack.

---

**How the Stack Works**

Normally, the kind of stack that a programmer might be used to using can be implemented as an array or linked list. The problem with this however is that *stack frames* come in many different sizes, depending on the requirements of the specific function, making standard arrays difficult to use. Untyped lists could solve that problem, but introduce significant overhead and require some kind of additional memory management.

So call stacks are implemented as just a big block of bytes and a pointer.

![Call Stacks](../../images/stacklayout.jpg)

The *stack pointer* (often abbreviated SP) is normally stored in a dedicated CPU register and stores a pointer to the top of the stack (represented by the arrow in the diagram). When a *stack frame* needs to be added, the number of bytes in the stack frame is added to *SP* (or subtracted, as many systems actually have the *call stack* grow *downward*), thus creating a new stack pointer. The current *stack frame* is placed in the space between the new and old *stack pointers*. Included in the *stack frame* is the old value of *SP* and the old *instruction pointer* (IP), which will both be reset when the function returns.

The top example in the diagram shows an empty portion of the stack.

The middle shows the stack after a new stack frame has been allocated.

The last example is important; it shows the stack after the frame has been popped off. This is done by resetting *SP* to its value prior to allocating the frame. Regardless of how big the frame is, all of it can be cleaned simply by changing *SP*, making it one of the simplest forms of garbage collection aside from unplugging your computer.

It's also important to also point out the limitations of a stack. A call stack, on its own, is actually more or less all that's necessary for Turing-completeness (in fact, there's a specific name for purely stack-based systems; *Stack Machines*). However, as Chris Domas's [Movfuscator](https://github.com/xoreaxeaxeax/movfuscator) shows, "Turing-Complete" does not necessarily mean "efficient". While memory based on a call stack can implement any program, and while the garbage collection is near-instantaneous, the problem that arises is from returning data from functions. It is possible to get around many of the above-mentioned limitations, and even be able to make arbitrary allocations, provided a big enough stack.

However, what happens if a function needs to return some data? If you happen to have space available for it in a previous stack frame, no problem. Just pass a pointer into the current function and use it to write the data there. However, what if we have to return a large data structure of unpredictable size, like a tree or graph? In this case, our only real option is to allocate it on the top of the stack and then *move* it. Moving complex data structures isn't simple; it requires going through every node of the structure, finding a new location for it, copying it, and also having data-structure-specific functions (that may have to work without a normal stack frame) to update any pointers so that they reference the new locations of all the connected nodes. Such a process is quite slow and complicated, especially if the function you're returning it to will itself return it (and so on).

The standard solution is the heap; a fairly unorganized portion of memory where individual chunks of memory have to be explicitly allocated and cleaned up, but where data can persist as long as necessary. There are other solutions (for example, Jonathan Blow's upcoming Jai language features "Temporary storage"; a secondary stack that can be explicitly pushed to and popped from, allowing data to be quickly allocated and freed, but also passed between function scopes without copying. This still has it's limitations however.)

---
