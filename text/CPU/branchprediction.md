# Branch Prediction and Speculative Execution

---

Note: this article talks a lot about [pipelines.](pipelines.md)

---

**The Problem**

Suppose we have a 10-stage pipeline full of instructions. The CPU is running our code when suddenly: we hit a branch instruction!

```

L0:
  **
  * Do Loop Stuff *
  **
  cmp r0 r3
  jl L0
  **
  * Do Stuff *
  **

```

In our 10-stage pipeline, the instructions the branch is dependent on are fetched 10 stages before they are written back to registers. Assuming that the branch immediately follows them, we come across a pretty big problem; we have to wait 10 cycles before we can figure out which code to fetch next! Because of the fact that branches show up about every 4-8 instructions in the average program, we may be spending more than half of our time just waiting to figure out which code to fetch next! That would slow down our CPUs quite a bit!

If this isn't bad enough already, it gets a lot worse in wide-issue CPUs; if the CPU is executing 2 or 3 instructions per cycle, we come across branches 2-3 times as frequently, making the relative cost 2-3 times higher!

So what can we do about it?

---

**Speculative Execution and its Advantages**

The solution is pretty simple; *Speculative Execution*.

Suppose that when we come across a branch instruction, we make a guess about which way we will go.

* If we guess right, then the effective cost of the branch (in terms of latency) is zero.

* If we guess wrong, we pay the full price of latency, and have just wasted time, power, and other resources on an incorrect guess.

If we make this choice completely randomly, we have a probability of guessing correctly 50% of the time, cutting the average latency of a branch down to just half (5 cycles). If we care more about improving speed than avoiding wasted power and resources, this is a great improvement! However, can we do better?

Most modern CPUs (with the exception of those in low-power devices) contain a branch predictor; a part of the processor designed to keep track of which way particular branch instructions *tend* to go, and in some predictors, under what circumstances branches go in each direction. By utilizing these, CPUs are able to dramatically cut down the costs of branches.

About 80% of time spent executing applications is spent on small inner loops, which typically contain just a single branch instruction at the end of each iteration that jumps back to the beginning of the loop to execute the next iteration. Since these loops tend to go the same route many times in a row, even very simple predictors can be up to 70-80% accurate.

---

**Speculative Execution and its Disadvantages**

The clear disadvantage of speculation is that sometimes we guess wrong, and those guesses can get expensive.

Modern CPUs are often very wide-issue, and very deeply pipelined. An x86 CPU in your laptop or desktop may contain 10 pipelines per core, each with 20 stages. These CPUs also often utilize Out-of-Order Execution, which tries to look for future instructions (often as far ahead as 100-200 instructions into the future) to run while it waits on memory and pipeline stalls from other instructions. This often requires that the CPU be able to predict branches very accurately; if it lists off the next 200 instructions it expects to run, and it turns out that one of the first branches it predicted was incorrect, it has to throw away all the work that came afterward, and redo it all. This can be extremely expensive, and so modern CPUs will often have very powerful branch predictors.

Also, if the CPU is not properly designed, some speculated instructions may create side effects with unintended consequences. For example, the *Meltdown* and *Spectre* hardware exploits take advantage of such unintended side effects.

---

**2-Bit Saturating Counter**

One simple type of branch predictor is a 2-Bit Saturating Counter.

Many branches are fairly predictable; if they go one way once, they're likely to go that way again. Such is the way with the branch at the end of a loop that conditionally jumps back to its beginning. A 2-Bit Saturating Counter does exactly what it sounds; it counts with 2-bit saturating arithmetic.

For reference, saturating arithmetic is a form of computer arithmetic where if the number gets too big it simply stays at the maximum value, and if it gets too small, it stays at the minimum value. This keeps the number line from wrapping around like a clock like is typical in computer arithmetic. A 2-bit binary number can only store a range of numbers from 0 to 3 (-2 to 1 if signed).

In this form of branch prediction, each branch is mapped to an entry in a small hash table. Each entry of the table stores one of these 2-bit counters, representing a number from 0 to 3. If the number is 0 or 1, the predictor predicts one path through the code. If it is 2 or 3, it predicts the other path. If the code actually takes the first path, the number is decremented regardless of the prediction. If the code takes the second path the counter is incremented, once again regardless of the prediction.

This technique is quite good at handling simple, predictable branches. If the branch reliably goes in one specific direction, this predictor will handle it nicely. The branch may even take the opposite path on a rare occasion (after all, if it never does, what's the point of the branch anyway?), but the counter has some tolerance built in to handle this. If the branch switches behavior, the counter can always adapt after only two examples.

The disadvantage of this type of predictor is that there are many branches that are slightly less predictable, or have patterns where they may frequently alternate between directions. In neither case does this predictor perform well. Regardless, it is a rather simple predictor that works reasonably well in low-end hardware.

---

**Two-Level Predictor**

While complex predictors can be more accurate than simpler ones, this often comes at the cost of performance. Complex predictors can be rather slow (high latency), and can significantly increase the length of the CPU pipeline. This creates a trade-off that a *Two-Level Predictor* takes into account.

A *Two-Level Predictor* is actually 3 different predictors in one; a fast and simple predictor, a slow and complex predictor, and a predictor that tries to predict which predictor is a better choice.

This third predictor doesn't predict the direction of branches by itself, and relies on the other two predictors to make such decisions. Essentially, it should have a bias toward the simpler predictor whenever said predictor is sufficient (for performance). For more difficult-to-predict branches, the accuracy of the simpler predictor may be too poor for the performance advantage to actually pay off (as mispredicts are very expensive), and so the complex predictor should be used then.

---

**Neural Network Predictors**

Some CPUs have resorted to using neural networks (most notably, many newer CPUs from AMD) for branch prediction. Once again, this maps branches to a hash table of predictors. The main difference is that this kind of predictor uses history information (1s and 0s representing whether or not the past N branches have been taken or not), and pass them as inputs for a small neural network. These kinds of predictors seem to be pretty accurate, though I have been unable to find precise numbers. 
