# Out of Order Execution

---


Most high-performance CPUs these days are [*Superscalar*](superscalar.md), issuing multiple instructions per core, per cycle. However, a problem arises with the sequential nature of computer code; how do we extract parallelism from a sequential stream of instructions? The most common solution is to use *Out-of-Order Execution*.

---

**The Basic Idea**

Instructions in computer code aren't strictly sequential, at least not in theory. We can often think of code instead as a *Dataflow Graph*, often full of many forks and joins. These forks and joins create opportunities for parallelism, and they show up on many different levels of our programs. We can zoom further and further into our programs and find more and more opportunities for parallelism. Even when we get to the smallest levels - individual instructions - we can often find quite a lot of parallel work.

So suppose we want to run multiple instructions in parallel to get better single-threaded performance. How do we find this parallelism?

A simple strategy is to load a long sequence of instructions into a large buffer, and track dependencies between the instructions. Whenever an instruction has all of its dependencies met, we can route it off to a pipeline for execution.


---

**Challenges of Out-of-Order**

There are several major issues that we face in designing an *Out-of-Order* CPU.

* Register Count and False Dependencies

* Branch Prediction

* Routing Hardware

* Ordering Memory Instructions
