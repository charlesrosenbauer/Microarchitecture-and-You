# Microarchitecture and You
**How your computer works, why you should care, and how that will change in the future.**


___
**Intro**

This started with a post I made on [FunFunForum](https://www.funfunforum.com) regarding how immutability is a very scalable approach to parallelism from a hardware perspective, and some people seemed to want a more thorough explanation of microarchitecture. So that's what this is.

I decided not to post this directly to FunFunForum though, as it's behind a paywall. If I'm going to help people with this information, I think it's best to make it freely available. I considered putting it on a blog, but decided Github would be better, as it would be more flexible and would allow for others more knowledgeable than me to contribute.

The explicit goal is this: **Explain how your computer works (CPU, GPU, RAM, etc.), how this affects programming, and how that will change in the future.**

So here's my plan: this will start out as a *relatively* brief explanation of microarchitecture, just going into the basics. However, much like wikipedia, this will be more of a collection of articles to provide more information on more specific subjects. Each article will be strewn with links to other articles where more information can be found.

Unlike wikipedia though, this will be more stripped down and to-the-point, more directed toward programmers, and trying to keep relavent to the overall goal of keeping to the aforementioned goal.

This does assume you understand binary, modular arithmetic, the concept of bits and bytes, etc.

At the moment, the following is the Table of Contents. Most of these articles have not been started yet, but I'll get to them as I can. Also, due to the intended nonlinear nature of this (I don't know what exactly to call this), this will be more of a list of subjects covered with links to those that have been completed. Feel free to suggest more subjects or contribute. Just be aware that this will take some time to complete. Articles with "(?)" afterward are ones I'm not sure about doing, as I don't know how interested people will be in them.

---
**Table of Contents**

(Note: Some articles may be mentioned multiple times due to the nonlinear structure of this.)

Introduction (You are Here)

Misc.
  * [Why You Should Care About Hardware](text/Misc/whyyoushouldcareabouthardware.md)
  * [Clock Speed v. Core Count](text/Misc/clockvcores.md)
  * [Amdahl's Law](text/Misc/amdahl.md)

Memory
  * [Why You Should Care About Memory](text/Memory/whyyoushouldcareaboutmemory.md)
  * RAM
  * Memory Alignment
  * [Common Types of Memory](text/Memory/commonmemory.md)
  * How Programs Lay Out Your Memory
  * Memory Bandwidth, Bus Size, and Latency
  * [Why Caches](text/Memory/whycaches.md)
  * [Caches](text/Memory/caches.md)
  * The Problem of Cache Coherency
  * SMP vs. NUMA
  * Virtual Memory
  * Memory Paging
  * Optimizing Around Latency
  * Optimizing Around Bandwidth
  * Caches v. Scratchpad v. Register Files
  * [Prefetching](text/Memory/prefetch.md)
  * Future of Memory

CPUs
  * Why You Should Care About the CPU
  * [Clock Speed v. Core Count](text/Misc/clockvcores.md)
  * CPU Fundamentals
  * Basics of Assembly
  * Logic Gates
  * Pipelines
  * Branch Prediction
  * Interacting With Memory
  * [Common Types of Memory](text/Memory/commonmemory.md)
  * [Prefetching](text/Memory/prefetch.md)
  * [RISC v. CISC](text/CPU/riscvcisc.md)
  * IPC and ILP
  * Superscalar Processors
  * SMT / Hyperthreading
  * Out-of-Order Execution
  * SIMD v. MIMD
  * Multicore and SMP
  * Manycore and NUMA
  * Heterogeous Architectures
  * Instruction v. Data Caches
  * Tiled Architectures (?)
  * VLIW : From DSPs to Itanium (?)
  * Stack Machines (?)
  * Belt Machines (?)
  * Future of CPUs

GPUs
  * Why You Should Care About the GPU
  * [Clock Speed v. Core Count](text/Misc/clockvcores.md)
  * GPU caches v. CPU caches
  * SIMD v. MIMD
  * GPU Register Files
  * [Common Types of Memory](text/Memory/commonmemory.md)
  * Caches v. Scratchpad v. Register Files
  * Basics of a Graphics Pipeline
  * Future of GPUs

Programming This Crazy Hardware
  * Minimizing Cache Misses
  * Working with the Prefetcher
  * Cache-Oblivious Programming
  * Intro to Bit Twiddling : Crazy Speed Hacks (?)

The Compiler
  * Various Optimizations
  * SSA Form : Functional Assembly
  * SIMD Optimizations (?)
