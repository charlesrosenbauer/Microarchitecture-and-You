# Superscalar CPUs

---

**Before we get Super, what is Scalar?**

Scalar values, for those who've forgotten their linear algebra etc., generally refers to a single, individual number.

A scalar CPU is a CPU that can issue and execute only a single operation at a time. [Pipelined CPUs](pipelines.md) can still be scalar despite technically executing multiple instructions in parallel, as they only *issue* a single instruction each clock cycle.

---

**Getting Super**

By extension, *Superscalar CPUs* are a step beyond *Scalar CPUs* in that they can issue and execute multiple instructions at a time. For example, look at the following code:

```
a = b + c;
f = d - e;
```

These two operations -- an add and a subtract -- are fully independent, and thus can be run in parallel. This kind of parallelism is quite common. Spinning up full threads for them would be costly and stupid, so how do we take advantage of this kind of parallelism?

In the past years, CPU clock speeds haven't gotten much higher. As of the time of writing (mid 2018), some high-end CPUs are barely dancing around in the 4-5 GHz range, though most CPUs remain *far* lower. These high speeds are mostly the result of excessive pipelining (more pipelining is *technically possible*, but branch prediction must be dramatically improved for the advantages to outweigh the costs). As a result, the vast majority of performance improvements in CPUs over the past decade or two have been the result of improved [IPC](ipcandilp.md).

---

**Complicating the Pipeline Story**

*Superscalar CPUs* don't have just one pipeline. Rather, each core features multiple that operate in parallel. If the CPU can issue two instructions per cycle, it may have two pipelines, one for each instruction.

There is often also specialization among pipelines; CPUs may feature pipelines specifically for integer math, or floating point math, or memory operations. A CPU may have multiple integer math pipelines, but only some of which may be capable of division (division hardware is expensive and sparsely used, so having less hardware dedicated to it might free up space for other things without a significant performance penalty).

Often, *Superscalar CPUs* may have more pipelines than they can even issue instructions to. For example, AMD's Zen architecture features 10 pipelines (4 Int, 4 Float, 2 Memory), but each Zen core can only issue 6 instructions at a time. This provides it some flexibility; exactly which types of instructions are being issued can vary. If there's only pipeline per instruction issued, but not enough of a specific type of pipeline, the CPU can't run all its instructions. A couple extra pipelines can offer a performance improvement here.


---

**Engineering Challenges**

The primary challenges seen with *Superscalar CPUs* are extracting the parallelism in the first place, and routing it out to the pipelines.

Most modern CPUs these days are based on simple instruction models where only one instruction is fed in at a time (in theory). However, this provides us no parallelism! Instead, modern CPUs cheat by trying to read in multiple instructions at a time and doing some dependency checking to locate any parallelism.

There are multiple approaches to this.

*In-Order CPUs* read and execute instructions in exactly the order given by the program. These CPUs can often be *multi-issue*, *dual-issue* being most common. In this case, they read in two instructions at a time, and if they can be run in parallel (and if all their dependencies are met of course), it sends them to different pipelines. If they can't, it just runs them sequentially. This has the advantage of being pretty efficient, but does not normally scale well past two or three instructions.

*Out-of-Order CPUs* read and execute instructions in whatever order they find most convenient. They fetch and decode often dozens to hundreds of instructions in advance (relying very heavily on branch prediction to decide exactly which path to take), map out all the dependencies between each instruction, and then issues as many as possible at a time. This has the advantage of scaling to larger amounts of ILP (4-8 is common), however comes at the cost of very power-hungry, complex hardware. Also, as a result of being mostly speculative, these CPUs frequently have to throw away dozens of instructions worth of work when the branch predictor makes a mistake. This is the primary type of CPU used in high-performance systems.

*VLIWs* are CPUs where the instruction set explicitly describes where instruction-level parallelism exists. This leaves the complex job of tracking dependencies and scheduling instructions up to the compiler, not the hardware. This is a technique that requires significantly less power and complexity than other techniques (since the CPU has a much simpler job), but can be very rigid and brittle; it does not typically deal well with complex control flow and unpredictable parallelism.

The other problem, routing instructions to pipelines, does not scale well. If the CPU has 4 instructions and 4 pipelines it can route them to, it must decide which pipeline to send each instruction, and how to get it there. With specialized pipelines, this can get complex. In *Out-of-Order CPUs*, which often have to pick a small handful of instructions out of a group of hundreds and route them to the pipelines, this problem gets very complex.
