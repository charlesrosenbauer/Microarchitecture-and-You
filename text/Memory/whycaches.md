# Why Caches?

---

Why do we need a cache?

Latency. While the CPU can push tons of requests to RAM every second, those requests take a while to get back. It winds up functioning a bit like a pipeline; memory requests can be put in frequently, but we don't get the results back until quite a bit later. This latency is mostly due to a few factors:

1. There is some distance between the CPU and RAM

2. Electrical signals don't travel instantaneously

3. The CPU runs fast enough that the resulting lag is pretty long

As a result, the hardware guys stick an extra block of memory inside the CPU to keep recently used data nearby, as it will probably be used again. This means next time it's needed, it can be accessed with much lower latency.

So how much latency are we talking about? 4 or 5 clock cycles? A dozen? A little more?

Accessing RAM takes **about 200-300 clock cycles.** More or less. The faster your CPU is, and the farther apart the CPU and RAM are, the higher that will be.

To put that into perspective, the following arbitrary piece of C code will probably complete in a similar amount of time:
```
int x = 0;
for (int i = 0; i < 50; i++){
  x = (x * 2) + i;
}
```

Think about that for a second. The above calculation can complete in less time than it takes to get something from RAM. In fact a pretty good compiler might even be able to make that code a few times faster than a RAM access.

Compare that to an L1 cache, which normally gets you your requested data back in only a clock cycle or two. That's a pretty big difference in performance.

[Okay, but how do caches work?](caches.md)
