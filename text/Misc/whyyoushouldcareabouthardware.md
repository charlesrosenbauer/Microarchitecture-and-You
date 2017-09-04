# Why You Should Care About Hardware

---

So, why should *you* care about hardware?

Well, you don't *have to*. You can get by perfectly fine as a programmer without knowing anything about how your computer works. After all, computers are getting faster all the time. Compilers are getting better at optimization. So long as you're not working with the most obscure cases that no one cares to optimize for, chances are good that code that runs like garbage now will run just fine in a few more years.

There's an old adage in the industry; *Andy giveth and Bill taketh away.* Andy Bryant being the CEO of Intel at the time, and Bill being Bill Gates of course. It's commonly said that what's even more impressive than the hardware industry creating hardware with exponentially growing performance is the ability for the software industry (that's you) to develop software that uses it all up.

Of course, knowing a thing or two about it won't hurt. More efficient code means you can do more with less. Convenience and elegance are great, but once in a while it becomes more important to have your code run fast than it is to make sure the code reads like poetry. In those cases, something like this hopefully will be able to help.

Now, maybe you're thinking to yourself "I work in Javascript! Everything's too abstracted away from the hardware for any of this to matter!", however it's important to remember that compiler's aren't magic. If you write an algorithm that works horribly with the cache, the compiler can do very little to fix that for you (with some minor exceptions made for Haskell). Even just a basic idea of how to iterate through an array in a prefetch-friendly way might help you write more efficient code. If you don't care for the nitty-gritty-microarchitectural-details, many of the articles here contain a "How to Utilize X Effectively" section that may still be of use.


[So why is memory important?](../Memory/whyyoushouldcareaboutmemory.md)

[So why is the CPU important?]()
