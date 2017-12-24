# Guidelines on Optimization

---

**Introduction**

A lot of these articles are here to inform you on how to better utilize your hardware. This is, in essence, a form of software optimization.

Optimization of software is becoming a bit of a lost art these days, with most developers caring to optimize their code only after they have hit a wall and optimization becomes *absolutely necessary*. It still remains common in HPC, Embedded systems, and Game development, but general software isn't being optimized as well as it could be.

Sure, hardware keeps getting faster (for now), and software developers keep wanting easier and easier to use languages and tools. Plus we can always resort to the "let's just let the guys working on lower levels solve it", pushing work toward API developers, driver developers, compiler developers, hardware developers, and so on. However, rather than force other people to pick up our slack (as this will only take us so far), it might not hurt to, at least occasionally, ask ourselves "Is there a more efficient way to do this?", even if performance isn't absolutely critical.

---

**Tradeoffs**

There are of course tradeoffs for optimizations.

Some of the crazier optimizations, bit twiddling for example, can provide significant performance improvements if used under the right circumstances. A good example is the *Fast Inverse Square Root*, which made Quake III Arena's lighting system possible on the hardware available back then. It approximated an inverse square root by performing a bit shift on the raw bits of a floating point number, applying an iteration or two of Newton's method, and throwing in a *Magic Constant*, chosen because it adjusted the result for errors reasonably well.

These kinds of optimizations clearly suffer a critical problem; while they can dramatically improve performance in the right cases, they can require substantial technical knowledge to understand. Not all developers have such knowledge, so it may make working with other developers a bit more challenging. They also may be more prone to difficult-to-fix bugs.

While this particular issue is most dramatic with more exotic techniques, optimized code often comes with tradeoffs related to readability. Changing how a loop accesses an array to make it more [cache-friendly](../Memory/caches.md) may not provide clear benefits to someone who doesn't understand caches. The best that can be done in these cases is to annotate the code with a comment noting the reasoning for the optimization, and what is going on. Maybe with a resource to help the reader understand the problem better.

Donald Knuth's classic adage, "Premature Optimization is the Root of All Evil" also may come to mind when considering costs of optimization. Performance comes at a cost, and putting too many resources into it too soon can slow down development and increase bugs. If you find some place in your code that could use an optimizaiton, it may be a good idea to make a note to optimize it later. It's probably better to get it working first before trying to get it to run fast. 

---

**Where to Focus Efforts**


---

**Profiling and Testing**
