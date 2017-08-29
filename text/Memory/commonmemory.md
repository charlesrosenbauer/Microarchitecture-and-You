# Common Types of Memory

---

This is list of common terms regarding types of memory and what they mean.

---

**SRAM**

SRAM is the kind of memory that makes up CPU registers and [caches](caches.md). It stands for *Static Random Access Memory.* It typically consists of a few logic gates, and around 6 transistors per bit. It is very fast, but very expensive. It also has low memory density, making it relatively low-capacity. It is also volatile, meaning that it loses all of its data when power is cut off.

---

**DRAM**

DRAM is the kind of memory that makes up RAM. It stands for *Dynamic Random Access Memory.* It typically consists of one transistor and one capacitor per bit. This makes it cheaper and denser than SRAM, though it is slower. Due to capacitor leakage, DRAM has to be "refreshed" several times per second to keep from losing its data. As a result, it is a form of volatile memory.

---

**VRAM**

VRAM is Video RAM. It's just DRAM tuned for use in GPUs. This typically means higher bus speeds and widths.

---

**NAND FLASH**

NAND Flash is the kind of memory commonly used in SSDs. It uses groups of NAND logic gates wired together to form memory cells. For a variety of reasons, this kind of memory can be made very dense and cheap. It is also a form of non-volatile memory, meaning that it can retain information without constant power. Despite all of this, it has a drawback of being very slow compared to DRAM and SRAM, and only being able to be written to a finite number of times before failing.

The amount of memory that can be stored in the logic gates is variable, but comes at a cost; multiple bits can be stored in a single gate via an analog signal, but doing so reduces the durability of the memory, causing it to wear out more quickly. High-capacity SSDs will typically have 2 or 3 bits per gate, while High-endurance SSDs will typically only store 1.

---

**HBM**

HBM, or High Bandwidth Memory, is a type of memory used in very high-end GPUs. It will likely become more common in the future. It works by placing the normal GPU die and several stacks of DRAM dies on a larger, cheaper die called an interposer. The interposer functions like a high-performance PCB, allowing larger bus sizes than would normally be practical. This allows for more bandwidth, lower RAM latency, and lower power due to smaller wires and the fact that it's [run slower than normal VRAM](../Misc/clockvcores.md).

---

**Volatile Memory**

Memory that loses its stored information in the absence of power.

---

**Non-Volatile Memory**

Memory that retains information, even without constant power.

---
