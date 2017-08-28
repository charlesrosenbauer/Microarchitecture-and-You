# RISC v. CISC

---

**So What Are RISC and CISC?**

RISC and CISC are types of CPU instruction sets. More or less.

RISC stands for "Reduced Instruction Set Computer". CISC stands for "Complex Instruction Set Computer". The obvious difference is that their instruction sets are designed a bit differently.


---

**RISC**

Typically, about 20% of a CPU's instructions will wind up doing about 80% of the work. RISC is based around the idea that the other 80% isn't absolutely necessary, and so can be stripped out to create a simpler CPU.

Pros | Cons
-----|------
Simpler Instruction Set | Less Performance than CISC
Cheaper to Design (simpler)| Fewer instructions available for optimizations
Cheaper to Manufacture (smaller)| .
Lower Power | .
Faster Decode Stage in the Pipeline | .

Examples of RISC instruction sets are ARM and MIPS.

---

**CISC**

CISC is based around the idea that certain instructions are frequently used together, and so why not combine them into one instruction for better performance?

Pros | Cons
-----|------
Very high performance | CPUs are large
More available compiler optimizations | High Development Costs (complex)
Instruction-level support for complex operations (sqrt, dot product, etc.) | High Manufacturing Costs (larger cores)
. | Very Power Hungry
. | Slow Decode Stage in the Pipeline
