# Reading Instructions

---

Fundamentally, modern CPUs work by reading a linear stream of instructions. But how does this work?

Note: In this article, I will specifically be talking about how this works in *Register Machines*, as they are the dominant paradigm in modern hardware. However, it's important to note that other systems such as *Stack Machines* and *Belt Machines* also exist, and work a bit differently.

In this article we will be designing a toy instruction set to demonstrate what is necessary to create a functional computer.

---

**Operations**

First off, the CPU has different operations it can perform on data; most ammount to math, memory operations, and control logic. Let's create a list of simple operations we might want:

```
ADD - add two numbers
SUB - subtract one number from another
MUL - multiply two numbers
DIV - divide one number by another
MOD - return the remainder after a division
CMP - compare two numbers and return any conditions
J   - unconditional jump
JLS - jump if less
JGT - jump if greater
JEQ - jump if equal
LOD - load value from memory
STR - store value to memory
```
