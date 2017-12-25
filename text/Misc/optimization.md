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

While this particular issue is most dramatic with more exotic techniques, optimized code often comes with tradeoffs related to readability. Changing how a loop accesses an array to make it more [cache-friendly](../Memory/caches.md) may not provide clear benefits to someone who doesn't understand caches. The best that can be done in these cases is to annotate the code with a comment noting the reasoning for the optimization, and what is going on. Maybe with a resource to help the reader understand the problem better. This may not always be possible, especially when optimization requires heavy changes (like switching between SOA and AOS). Some consideration to how important optimizing a particular part of the application can go a long way sometimes.

Tony Hoare's classic adage, popularized by Donald Knuth, "Premature Optimization is the Root of All Evil" also may come to mind when considering costs of optimization. However, it's important to remember that this is the abbreviated version. The full version is "We should forget about small efficiencies, say about 97% of the time: premature optimization is the root of all evil. Yet we should not pass up our opportunities in that critical 3%".

It's important to not get bogged down trying to optimize everything (after all, the compiler isn't too bad at that in simple cases), but we should definitely consider optimizing the critical parts of our applications. Make sure the overall applicationa architecture is efficient, and make sure that the most commonly run code runs fast.

---

**Where/How to Focus Efforts**

Code often follows the Pareto principle, also known as the 80/20 rule. In this case, 80% of execution time is spent on 20% of the code. Most of this code tends to be specifically be innermost loops of frequently called functions. These are the places where performance tends to be most critical. Some understanding of [Amdahl's Law](amdahl.md) is very useful here.

It's also always important to remember to always verify that optimizations improve performance. Computers are very complex beasts these days, and the vast number of different factors can make predictions about performance very difficult to make. Therefore, it is never enough to just write an optimization, but it is also important to verify that it actually is an effective optimization. It may be that, due to factors beyond your control, or created by other parts of the application, or even created by other active applications, an optimization may fail to actually improve performance. It may also be that the effectiveness of the optimization may vary over time; the optimization may not improve performance now, but due to a change somewhere else later on, the optimization may become effective again. An understanding of hardware is of course a great way to make assessments on this, though it may occasionally still be difficult to assess exactly why an optimization may not be working well.

Luckily, there are tools to make this process easier. *Profilers* are incredibly valuable tools for analyzing the performance of different parts of your application. They can serve as a great guide, showing you what parts of your application are most in need of optimization, and how the relative performance of different parts of the application vary as your application evolves.
