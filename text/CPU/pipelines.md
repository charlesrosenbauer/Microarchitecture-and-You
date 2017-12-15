# Pipelines and Execution

---

**Execution and Issues with the Clock**

Every clock cycle, the CPU reads in an instruction, decodes it, and then executes it (very simplified!).

However, some instructions are more complicated than others. For example, multiplying two numbers is a lot more work than just adding or subtracting two numbers. Dividing two numbers is a similar amount of work, but with fewer opportunities for parallelism than multiplication, making it very slow. All of these are more complicated than a nop (pronounced no-op) which, as its name may suggest, doesn't do much.

Okay, so some instructions take longer than others to calculate. Some instructions therefore take multiple clock cycles. However, it's also important to note that loading instructions from memory and decoding them also takes time and computing power, oftentimes even more than executing the actual instructions themselves!



---
