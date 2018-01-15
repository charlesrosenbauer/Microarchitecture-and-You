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
