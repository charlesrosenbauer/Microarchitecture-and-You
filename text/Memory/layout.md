# Why Memory Layout is Important

---

This is a really important subject. It's also a really big subject, so it'll probably be split into multiple smaller articles.

But why is it so important? Why is the way we organize memory so important? Simple; [memory latency](whycaches.md) makes data structures more than ten times more important than algorithms (with some asterisks).

---

**Data Structures v. Algorithms**

As these two things are both extremely important aspects of computer science, and while they can somewhat be seen as two sides of the same coin, it may seem controversial to claim that one is more important than the other, especially to claim figures like "more than ten times". So here is my derivation:

First off, it's important to note that data structures and algorithms are both subject to Big-O-Notation time complexity. This means you may have two algorithms/data structures that produce the same results, but with tremendously different levels of efficiency.

For example, if you have an array of items and you wish to find a specific item from it, you could iterate through it and check each item until you find a match, but this on average takes N/2 checks, where N is the number of items in the array. However, if you use a *Sorted Array*, or load the values into a *Binary Tree* or *Hash Table*, then suddenly you can perform a *Binary Search* and the number of items you need to check scales with logarithmic time. For those unfamiliar with the term, this means that in linear increases in the amount of work required to get a result require exponential increases in the size of the dataset. For example, if searching through 2 items takes 1 step, searching through 4 will take 2, <128 will take <7, and a <1,125,899,906,842,624 will require <50. That's pretty good scalability.

This isn't just limited to data structures however; many numerical algorithms can be seen as searching through a number line for a result. The *Bisection method* is a common way to accelerate such algorithms if they meet certain criteria. In fact, the *Bisection method* is analogous to a *Binary Search* on the number line. For example, if we have a very fast multiplication function, but no fast square root, we can evaluate a square root of some number N by using a binary search on the number line and checking if each number is a "match" by squaring it and checking if the result is equal to N (within a margin of error, as rounding errors will often prevent us from finding an exact result). Any "match" we find will be the square root (within our margin of error) of N, and will be found in logarithmic time (proportional to the number of bits of precision minus the logarithm of our margin of error).

Many of such equivalences show up between data structures and algorithms. In fact, based on the above example, it may even be reasonable to claim that this is because the two are the same kind of thing. However, we need to draw some kind of distinction between the two. After all, we could consider a *Sorted Array* to be a Data Structure, but the *Binary Sort* we use it for an algorithm, right?

So let's draw a distinction here; for the purpose of this argument, when I refer to "algorithms", I'm referring specifically to algorithms that work on fixed amounts of data. For example, the *Bijection Method Square Root* mentioned above only takes a single number as an input. Meanwhile, a *Binary Search* takes a *Sorted Array* of arbitrary size as an input, and thus will be, for the sake of this argument, classified as an aspect of a data structure. After all, the search wouldn't work without data being structured as it is. This of course makes a very blurry line between the two, but of course, but that's not too important for most cases.

So, given these slightly adjusted definitions of algorithms and data structures, *why are data structures more important?*

Seeing as both feature time complexity as an aspect, and analogous algorithms and data structures, complexity wise, are common, we can factor that out. So we're left wondering, how much does each iteration cost in the cases of algorithms/data structures, worst case scenario?

Well, if you screw up an algorithm implementation, worst case scenario gives you code that is extremely sequential; each CPU instruction depends on the last, and must wait for it to exit the CPU pipeline. These days, most CPUs have pipelines with around 10-15 execution stages, meaning we get about a 10-15x slowdown in the worst case. However, the vast majority of code has a lot of instruction-level parallelism, and both the CPU and compiler are working very hard to utilize it wherever possible, so in practice, creating such "worst-case-scenario" code would actually be incredibly difficult.

On the other hand, if you screw up a data structure, any unnecesary memory transaction is *very expensive*. It can easily be on the order of 300 clock cycles if it's outside the cache (which will be very common if your dataset is big enough). Even if half our RAM requests hit the cache, our best case scenario is still ten times worse than the difficult-to-achieve worst-case-scenario of algorithms, and is in many cases even an inevitability.

So data structures are *at least* ten times more important than algorithms.
