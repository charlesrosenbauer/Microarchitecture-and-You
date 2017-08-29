# Clock Speed v. Core Count

---

In a perfect world, we could just get infinitely fast CPUs; any problem a dual-core CPU can solve, a single-core CPU can solve in a similar amount of time if running at twice the clock speed. In fact, since it doesn't have to perform any synchronization between cores, and because some algorithms require less work when run sequentially, the single-core CPU should actually require *less* work.

So why do we have multiple cores then? Wouldn't it be better to just get higher clock speeds?

---

**Basic physics lesson:**

Power consumption of a CPU follows the following formula: P = V^2 C F

Variable | Approximate Meaning
--|--
P | power consumption in Watts
V | Volts (how much "electrical pressure" are we using)
C | Capacitance (volume of circuitry of currently powered)
F | Frequency (clock speed)

The above "meanings" aren't strictly true. I'm no electrical engineer, but these are as accurate as they need to be for this explanation.

As you can see, power consumption scales linearly with capacitance and frequency, and scales quadratically with voltage.

It is also important to point out that voltage scales roughly linearly with frequency. Due to factors like electrical leakage, it tends to increase at a faster rate than the frequency (especially when using very small transistors), but it's more or less the same.

As a result, we can rewrite the formula to: P ~ F^3 C

In plain English: twice the clock speed means twice the power, but twice the clock speed means a hell of a lot more than twice the power.

In the real world, due to various factors (such as leakage throwing off our numbers a bit), a 30% drop in clock speed typically lends a 50% drop in power consumption. A single-core CPU at 1 GHz should be expected to use the same amount of power as a dual-core CPU at 700MHz, despite being at a 40% disadvantage in theoretical performace. This means that the long-term trend of hardware will likely be toward large numbers of very slow cores, as it is best for power efficiency.

---

**The Future is Heterogeous**

One clear issue is that not all code can be run in parallel. Some code has to run sequentially. It's important to keep [Amdahl's Law]() in mind when considering maximizing performance.

A good solution to this is ARM's big.LITTLE architecture. big.LITTLE features a large number of small, simple, slow cores, and a small number of large, complex, fast cores. This means that parallel work can be run on the numerous slower cores while the sequential work can be run on the few fast cores. This is a pretty efficient approach to maintaining efficiency while minimizing the overall latency of the software.
