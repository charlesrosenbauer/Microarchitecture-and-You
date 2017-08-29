# Prefetching Memory

---

One drawback of caches is that they only store data that has been recently loaded. But what about memory that hasn't been loaded yet? How can we get good performance from that?

```C

int array [1000000];  //Suppose this is preloaded with data

for(int i = 0; i < 1000000; i++){
  array[i] += 1;
}

```

The above code, for those unfamilar with C, iterates through an array of integers, adding 1 to each element. Assuming that these are 64 bit integers, the array will take up about 8 MB.

With [64 byte cache lines](caches.md), we'll only get a cache miss every eighth iteration. Still, that's a lot of cache misses. If each iteration only takes 1 clock cycle and RAM latency is only 200 cycles, all 1 million iterations will take 26 million clock cycles. If we had no cache misses, then it would take 1 cycle per clock, and thus would run 26x faster.

Well, most CPUs with caches these days actually have a way around this; Prefetch instructions!

---

**Prefetch Instructions**

```C

int array [1000000];  //Suppose this is preloaded with data

for(int i = 0; i < 1000000; i++){
  prefetch(array[i+1]);
  array[i] += 1;
}

```

Let's pretend that C has a "prefetch" function. And let's also disregard any bugs that might exist due to the sloppy "array[i+1]" part, or the fact that 64 byte cache lines mean the prefech only needs to happen every eighth iteration.

Prefetch instructions allow the CPU to tell the cache to load stuff for us, as we'll probably need it soon.

However, looking at the code above and taking into account latencies, we have a minor issue; prefechting takes 200 cycles here, but each cache line is fully used after only 8. As a result, we'd be better off prefechting memory much further than one ahead to make sure it's here when we need it. For this case, we'd be best loading memory about 25 cache lines ahead (1600 bytes).

Now you may be thinking; "Isn't this really dependent on timing to maximize efficiency? How do I know that data ahead of time?"
The answers to those questions are "Yes", and "You normally can't," respectively.

So how does this help us? Prefetch instructions are good in some cases, but normally aren't very useful. Normally you can assume that if they are useful in a simple situation, the compiler might be putting them in for you. However, the hardware guys have a solution; *Hardware Prefechting.*

---

**Hardware Prefetching**

The basic idea behind hardware prefechting is that the CPU will try to predict what memory you'll need in the near future, and prefetch it for you.

One simple way it can do this is to detect if you've used multiple cache lines in a row. In the case of our C code above, the CPU might detect that we're iterating through memory, and start loading cache lines for us.

A slightly more complex method is that the CPU can detect patterns in the offset of subsequent memory requests; if you request the byte at address 100, then address 110, then 120, etc., it might start predicting that you're loading each 10th byte, and start prefetching memory accordingly.

More complex methods than that exist too. Some CPUs use Correlating Prefetchers, which create a small table of a few hundred commonly requested memory addresses and what a few addresses commonly called afterward. Delta Correlating Prefetchers are similar, but use the difference between requested memory addresses rather than exact addresses. Some CPUs (for example recent AMD CPUs) even use neural networks to predict future memory requests.
