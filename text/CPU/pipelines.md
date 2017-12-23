# Pipelines and Execution

---

**Execution and the Clock**

Every clock cycle, the CPU reads in an instruction, decodes it, and then executes it (very simplified!).

However, some instructions are more complicated than others. For example, multiplying two numbers is a lot more work than just adding or subtracting two numbers. Dividing two numbers is a similar amount of work, but with fewer opportunities for parallelism than multiplication, making it very slow. All of these are more complicated than a nop (pronounced no-op) which, as its name may suggest, doesn't do much.

Okay, so some instructions take longer than others to calculate. Some instructions therefore take multiple clock cycles. However, it's also important to note that loading instructions from memory and decoding them also takes time and computing power, oftentimes even more than executing the actual instructions themselves!

The simplest way to do this is to split the CPU into different components that each do a different task, and have some timing-based system (the *Control Unit*) keep track of how to coordinate it. For example, the following is how an addition instruction may be executed:

Cycle 0 : Load an instruction from memory

Cycle 1 : Decode the instruction. The instruction tells us to add registers A and B, and store the result in C.

Cycle 2 : Connect registers A and B to an *Arithmetic Logic Unit (ALU)* and instruct it to add its inputs. It performs the task.

Cycle 3 : Get the results from the *ALU* and store the result in register C. At the same time, increment the *Instruction Pointer* so that the *Control Unit* can fetch the next instruction.

--

Let's compare that to how a multiply might work:

Cycle 0 : Load an instruction from memory

Cycle 1 : Decode the instruction. The instruction tells us to multiply registers A and B, and store the result in C.

Cycle 2 : Connect registers A and B to an *Arithmetic Logic Unit (ALU)* and instruct it to multiply its inputs. It begins to perform the task.

Cycle 3 : The multiplier grinds away at its work.

Cycle 4 : The multiplier grinds away at its work some more.

Cycle 5 : The multiplier grinds away at its work a while longer, and finishes up.

Cycle 6 : Get the results from the *ALU* and store the result in register C. At the same time, increment the *Instruction Pointer* so that the *Control Unit* can fetch the next instruction.

--

As can be noted, the CPU takes a bit longer to multiply than it does to add. However, consider what is going on in the fetch and decode units during the execution stage; nothing. These parts of the CPU are simply idling around, twiddling their thumbs with nothing to do! what if we could keep them occupied and give them useful work to do? This is the basic principle of CPU pipelining.

---

**The Pipeline**

A simple way to think of this is like an assembly line; each instruction can be split into different stages, and different stages of different instructions can be run in parallel, each in their corresponding part of the CPU.

This leads to a tremendous improvement in performance. For example, suppose we had a CPU where all instructions took exactly 5 cycles (for simplicity). Let's also disregard any effects of branches. Being able to execute them in a pipeline provides us with a 5x improvement in throughput! A larger number of pipeline stages gives us even more of an improvement!

In practice, things get a bit more complicated, but in general, we get much more performance, and the average effective latency of each instruction tends toward 1.

![Pipeline](../../images/pipeline.jpg)

CPUs are typically split into several pipeline stages, each with a different name and function. Though this can definitely be quite flexible, and exact stages vary from CPU to CPU, modern CPUs typically have *FETCH*, *DECODE*, *SCHEDULE*, *EXECUTE*, and *WRITEBACK*. This of course varies, as most modern high-end CPUs have 10-20 stages (for more throughput!) and different architectures will emphasize certain functionality more than others. For example, most early [RISC CPUs](riscvcisc.md) used only 5 pipeline stages; *FETCH*, *DECODE*, *EXECUTE*, *MEMORY ACCESS*, and *WRITEBACK*, where *MEMORY ACCESS* is specifically used for interactions with memory.

The *SCHEDULE* stage(s) in modern CPUs is due to the complexity created from Out-of-Order Execution, and the fact that modern CPUs tend to be very superscalar, where they may in fact fetch/decode/execute multiple instructions per cycle, and utilize more than one pipeline running in parallel. The exact details of this are beyond the scope of this specific article however.

![Pipeline Stages](../../images/pipelinestages.jpg)


---

**Hazards!**

One concern with a pipeline is the presence of *Hazards.* *Hazards* are dependencies between individual instructions in the pipeline that prevent instructions from being issued right away. For example, suppose we have the following set of instructions:

```
a = b + c
f = d * e
g = a + f
```

Cycle 0: Our CPU first isses an instruction to add **b** and **c**, storing the result in **a**. Let's say addition takes 1 cycle.

Cycle 1: The CPU issues an instruction to multiply **d** and **e**, storing the result in **f**. Let's say multiplication has 3 execution cycles. In this cycle, the CPU also finishes executing the previous instruction (as it only took 1 cycle).

Cycle 2: The CPU attempts to issue an instruction to add **a** and **f**. However, while **a** has been evaluted, **f** has not, as the multiplication will take another two cycles. As a result, the CPU cannot continue until the multiply has completed. Nothing is put into the pipeline (unless the CPU can find other work to do in the meantime). The empty space in the pipeline is often referred to as a *Bubble*.

Cycle 3: The CPU continues to wait, as the multiply instruction finishes up.

Cycle 4: Now that both previous dependencies have been completed, the third instruction (**g** = **a** + **f**) can finally be issued.

Cycle 5: The value of **g** is now available.

--

Hazards can slow down execution if the CPU is unable to find other work to do in the meantime. A lot of work on modern CPUs comes down to maximizing [IPC](ipcandilp.md) by finding other work for the CPU to do in the meantime. Modern CPUs also often feature multiple parallel pipelines, making the problem more complicated.

One simple solution to this can actually be done by the compiler, which is *Instruction Scheduling*. Simply put, most code has some extent to which instructions can be rearranged without changing the outcome of the code. In the above example, the first and second instructions can actually be swapped, as there are no dependencies between the two. In fact, swapping them would even reduce the number of cycles that the CPU would take to run the above code. I'll leave the explanation up to the reader to figure out. By cleverly arranging instructions such that the instructions with high latency (like multiply) are not immediately followed by instructions dependent on them, *Instruction Scheduling* makes sure that most of the time, the CPU does not need to look hard for other work to do while waiting on slower instructions.

---

**Bypasses and Stalls**

Suppose we have a CPU with the following instruction set and latencies:

Instruction | Number of Execution cycles
------------|---------------------------
Add         | 1
Subtract    | 1
Multiply    | 3
Divide      | 10
Compare     | 1
Branch      | 2
Jump        | 2
Load        | 3+
Store       | 1

(For those unaware, *compare*, *branch* and *jump* would be for control flow, and *load* and *store* would be for memory operations. Loads will take a different amount of time depending on where the data being loaded is in the [memory hierarchy](../Memory/caches.md).)

However, if the *WRITEBACK* stage of the pipeline is all the way at the end, and if all of these instructions go through the same pipeline, there seems to be a bit of a problem. The *divide* instruction seems to require that the pipeline contain at least 10 execution stages. Assuming we also have *FETCH*, *DECODE*, *SCHEDULE* and *WRITEBACK* stages, that makes our CPU pipeline *14 stages long!* And, since *WRITEBACK* is at the end of that, that makes all of these instructions, including fast ones like *add* also 14 stages long. That makes *hazards* much harder to deal with.

The most commonly used solution to this problem is to use *Bypasses*. Simply put, if the CPU detects that the instruction it wants to issue depends on an instruction that, while still in the pipeline, finishes early (like *add*), it can tell the instruction to read the result *directly from the pipeline*, rather than having it wait until the result is written to a register. Each pipeline stage may simply cache its previous result in order to make it available to the following instruction. Utilizing this of course either requires a smarter CPU or an instruction set with support for this, and backwards compatibility strongly encourages the first.

--

Memory creates another problem, as the exact amount of time to load memory can vary significantly. In the case of memory loads, the CPU will typically stop reading instructions in and wait until the data it needs becomes available. This is called a stall. If the CPU can find other work to do in the meantime, it may do that work while it waits, but otherwise the CPU will be forced to stall.

---

**Branches**

```C
if(condition){
  Path A
}else{
  Path B
}
```

Branches in a pipeline complicate things. With a branch, you have no idea what to put into the pipeline; it could be the code in **Path A**, or in **Path B**. If you have a 20 stage pipeline, you'll be waiting 20 clock cycles before you know which instruction to fetch next. That's an expensive branch.

Suppose we pick a direction. If we pick it at random, then 50% of the time we'll get it right and the branch will cost nothing. The other 50% of the time we have to throw away all the work we've done since the branch. Luckily, this cuts the cost of the branch in half; while a mispredicted branch will be expensive considering power consumption and latency, the predicted one is pretty cheap. Doing this cuts the branch penalty in a 20-stage pipeline to just 10 cycles (on average).

If we have a more accurate predictor, this gets a lot more accurate:

```C
cost = (costOfPrediction * predictorAccuracy) + (costOfMispredict * (1 - predictorAccuracy));

//Example:
costOfPrediction  = 0;
costOfMispredict  = 20;
predictorAccuracy = 0.95;

cost = (0 * 0.95) + (20 * 0.05);
cost = 1;

//Another example:
costOfPrediction  = 0;
costOfMispredict  = 20;
predictorAccuracy = 0.6;

cost = (0 * 0.6) + (20 * 0.4);
cost = 8;

//If we come across a branch every N cycles, we can derive the following
//performance numbers from Amdahl's Law:
cost = 1 + (costPerBranch * frequencyOfBranches);

//Example with a branch cost of 1 cycle, and branches every 5 cycles:
costPerBranch = 1;
frequencyOfBranches = 0.2;
cost = 1 + (1 * 0.2);
cost = 1.2;   // 17% of time will be spent on mispredicts.

//Example with a branch cost of 8 cycles, and branches every 5 cycles:
costPerBranch = 8;
frequencyOfBranches = 0.2;
cost = 1 + (8 * 0.2);
cost = 2.6;   // 62% of time will be spent on mispredicts.

//Example with a branch cost of 0.3 cycles, and branches every 5 cycles:
costPerBranch = 0.3;
frequencyOfBranches = 0.2;
cost = 1 + (0.3 * 0.2);
cost = 1.06;  //  6% of time will be spent on mispredicts.

//This shows why modern CPUs need really good branch prediction.
```
[Article on Amdahl's Law](../Misc/amdahl.md)

The cost increases substantially if we are executing multiple instructions per cycle. Power cost increases are pretty obvious; we're throwing away more instructions. Performance costs are less obvious, but mostly come down to the fact that we can expect a branch every 4-8 instructions, and so the more instructions we are executing in parallel, the faster we'll come across new branches, and the more likely it will be that one of the branches in the pipeline will incorrect.

As a result modern CPUs, which often issue 4-8 instructions per cycle and have 15-20 stage pipelines, often need extraordinarily advanced branch predictors to minimize mispredicts.

---

**Optimizations**

The best optimization to improve pipelining in your code (if you're not working with assembly) is to try to remove branches from your code. If you can write your code so that it does not contain any branches, it will likely perform a bit faster, especially if the branch is unlikely to go the same direction in most cases. If this isn't possible, try making your branches as predictable as possible; a branch that goes the same direction 90% of the time will be much easier to predict than one closer to 50/50, and thus will yield much better performance.

Most techniques for removing branches come down to either math tricks, [SIMD condition masks](simdvmimd.md), or bit twiddling tricks. These oftentimes come at the cost of readability, but can be useful, especially in small, inner loops. If each branch is fairly expensive, this may be more expensive, so beware. For example:

```C
int x;
if(unpredictableCondition){
  x = a;
}else{
  x = b;
}

//If unpredictableCondition can be converted to a 1 or 0 (1 for true, 0 for
//false) the following optimization can be used:

x = (unpredictableCondition * a) - ((1 - unpredictableCondition) * b);

//As multiplication can be expensive, bitwise operations can be used instead.
//Rather than 1 or 0, unpredictableCondition must either be 0 or fully saturated
//(11111111 for an 8 bit value, etc.) in order for this to work.
//In this case, all the bits in the value (a or b) are set to 0 if the value is
//not needed, and the value for each is combined with a bitwise OR.

x = (unpredictableCondition & a) | ((~unpredictableCondition) & b);

//Always remember to annotate these kinds of optimizations. They may not be easy
//for your coworkers and collaborators to understand. Also, always remember to
//profile your code to make sure that the optimization actually pays off. There
//are so many factors here that it can be hard to predict actual performance
//without actually running the code.
```

Sometimes CPUs can even support *Predicated Instructions*, which are instructions that only run if a certain condition is met. These avoid use of the branch predictor (since they don't affect control flow). Your compiler may implement these as an alternative to certain small branches. However, while ARM CPUs (like in your phone) have extensive support for these, x86 (desktop/server CPUs) only features a conditional move instruction, and it's limited exclusively to working within registers. It essentially amounts to the following code:

```C
int x = (cond)? a : b;

//Or alternatively,

int x;
if(cond){
  x = a;
}else{
  x = b;
}

//a and b must be either integers, floats, booleans, or pointers
```
