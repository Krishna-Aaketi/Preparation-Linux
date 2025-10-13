# 🧩 Memory Management (Subsystem 2 of 10)

---

## 1️⃣ What is memory management?
**Memory management** is the process by which the OS allocates, tracks, and reclaims memory for processes and kernel operations, ensuring safe and efficient use of available RAM.

---

## 2️⃣ What are the goals of memory management?
- Efficient memory utilization  
- Isolation between processes  
- Fast allocation/deallocation  
- Support for virtual memory  
- Prevention of memory leaks and fragmentation  

---

## 3️⃣ What is virtual memory?
**Virtual memory** gives each process the illusion of having a large, continuous address space even though the actual physical memory may be fragmented or limited.  
It relies on **paging** and **swapping**.

---

## 4️⃣ Difference between virtual and physical memory

| Feature | Virtual Memory | Physical Memory |
|:--|:--|:--|
| Location | Logical (software view) | Actual RAM chips |
| Size | Large (can use swap) | Limited by hardware |
| Managed by | OS + MMU | Hardware |
| Fragmentation | Can be non-contiguous | Must be mapped correctly |

---

## 5️⃣ What is a page in virtual memory?
A **page** is a fixed-size block of virtual memory (commonly 4 KB).  
Pages are mapped to frames in physical memory by the **MMU**.

---

## 6️⃣ What is a frame?
A **frame** is a fixed-size block of physical memory corresponding to a virtual page.  
Page ↔ Frame mapping is maintained by the **page table**.

---

## 7️⃣ What is a page table?
A data structure maintained by the OS that maps **virtual page numbers** to **physical frame numbers**.  
Each process has its own page table.

---

## 8️⃣ What is the Memory Management Unit (MMU)?
A **hardware component** that translates virtual addresses to physical addresses using **page tables** and caches like the **TLB**.

---

## 9️⃣ What is a TLB (Translation Lookaside Buffer)?
A **cache inside the MMU** that stores recent virtual→physical address translations for faster access.  
A TLB miss triggers a page-table lookup.

---

## 🔟 What is a page fault?
Occurs when a process accesses a page not currently in physical memory.  
The OS loads it from disk (swap or file) and updates the page table.

---

## 11️⃣ What is demand paging?
A scheme where pages are loaded into RAM **only when needed** rather than preloaded — improves performance and saves memory.

---

## 12️⃣ What is a segmentation fault?
An error raised when a process tries to access invalid or unauthorized memory (e.g., **NULL pointer dereference**).

---

## 13️⃣ Difference between paging and segmentation

| Feature | Paging | Segmentation |
|:--|:--|:--|
| Division type | Fixed-size pages | Variable-size segments |
| View | Physical | Logical (code, data, stack) |
| Fragmentation | Avoids external | May cause external |
| Protection | Page-level | Segment-level |

---

## 14️⃣ What is fragmentation?
**Wasted memory space** caused by inefficient allocation.

- **Internal fragmentation** → unused space inside allocated blocks.  
- **External fragmentation** → free space scattered into small chunks.

---

## 15️⃣ How to reduce fragmentation?
- Use **paging** instead of segmentation.  
- Use **compaction** to combine free areas.  
- Apply **buddy system** or **slab allocator**.

---

## 16️⃣ What is compaction?
Rearranging allocated memory to form one large contiguous free block.  
Used to reduce **external fragmentation**.

---

## 17️⃣ What is thrashing?
When the system spends more time **swapping pages** in/out of memory than executing actual processes — often due to insufficient RAM or excessive multitasking.

---

## 18️⃣ What is swapping?
Moving an entire process’s memory to disk (swap area) to free RAM, and restoring it when needed.

---

## 19️⃣ What are page replacement algorithms?
Algorithms the OS uses to decide which page to remove when memory is full:
- FIFO (First In First Out)  
- LRU (Least Recently Used)  
- Optimal (theoretical)  
- Clock / Second-Chance  

---

## 20️⃣ Explain FIFO Page Replacement.
Replaces the **oldest page** in memory first.  
Simple but may remove frequently used pages.

---

## 21️⃣ Explain LRU (Least Recently Used) Page Replacement.
Replaces the page that hasn’t been used for the longest time — a good approximation of the optimal algorithm.

---

## 22️⃣ What is the Optimal page replacement algorithm?
Replaces the page that **will not be used for the longest future time**.  
Best theoretical performance, but impractical since future access patterns are unknown.

---

## 23️⃣ What is the Clock algorithm?
Uses a **circular buffer (“clock hand”)** with reference bits to give each page a second chance before replacement — efficient and practical.

---

## 24️⃣ What is the Working Set Model?
Defines the set of pages a process is actively using.  
Pages outside this set are candidates for swapping.

---

## 25️⃣ What is Copy-On-Write (COW)?
A technique where pages are **shared** between parent and child after a `fork()`, and copied only when one of them modifies the page — saves memory.

---

## 26️⃣ What is the role of the kernel in memory management?
- Allocates memory to processes  
- Tracks page mappings  
- Handles page faults and swapping  
- Protects process address spaces  
- Manages caches and buffers  

---

## 27️⃣ What is mmap()?
A system call that maps **files or devices** directly into a process’s address space, enabling **memory-mapped I/O** and shared memory IPC.

---

## 28️⃣ What is memory-mapped I/O?
Device registers are mapped into the process’s address space, allowing direct access via pointers:
```c
*ptr = val;
````

---

## 29️⃣ What is the buddy system?

A memory allocation algorithm that divides free memory into **power-of-two blocks** to minimize fragmentation and support fast allocation/deallocation.

---

## 30️⃣ What is the slab allocator?

Used by the Linux kernel to manage **small objects** (like task structs, inodes).
Preallocates object caches to reduce overhead and fragmentation.

---

## 31️⃣ What is malloc() vs kmalloc()?

| Function  | Space        | Description                            |
| :-------- | :----------- | :------------------------------------- |
| malloc()  | User space   | Allocates from process heap using libc |
| kmalloc() | Kernel space | Allocates memory inside the kernel     |

---

## 32️⃣ What is heap vs stack memory?

| Feature       | Heap         | Stack          |
| :------------ | :----------- | :------------- |
| Managed by    | Programmer   | Compiler       |
| Lifetime      | Dynamic      | Automatic      |
| Size          | Large        | Limited        |
| Growth        | Upward       | Downward       |
| Common issues | Memory leaks | Stack overflow |

---

## 33️⃣ What are memory leaks?

Allocated memory that is never freed, causing resource waste and potential slowdown.

---

## 34️⃣ What is virtual address space layout (Linux 64-bit)?

| Region    | Purpose                                |
| :-------- | :------------------------------------- |
| `0x0000…` | User text, data, heap                  |
| `0x7fff…` | Stack (grows downward)                 |
| `0xffff…` | Kernel space (shared across processes) |

---

## 35️⃣ What is Address Translation?

The MMU splits a virtual address into:

* **Page number** → used to look up in page table
* **Offset** → added to frame base to form physical address

---

## 36️⃣ What is a page directory and hierarchy?

Modern systems use **multi-level page tables** (e.g., 4-level: PGD → PUD → PMD → PTE) to efficiently manage large address spaces.

---

## 37️⃣ What is the difference between shared and private memory mappings?

| Type            | Description                                          |
| :-------------- | :--------------------------------------------------- |
| Shared mapping  | Changes visible to all processes                     |
| Private mapping | Each process gets its own copy on modification (COW) |

---

## 38️⃣ What is brk() and sbrk()?

System calls that adjust the end of the process’s data segment (heap).
Used internally by `malloc()` for dynamic memory allocation.

---

## 39️⃣ What is a Page Frame Number (PFN)?

An **index identifying a physical frame** in RAM; used by the kernel to manage physical memory pages.

---

## 40️⃣ How does Linux track free/used memory?

Through:

* **Zone allocator** (DMA, Normal, HighMem)
* **Buddy system**
* **Page frame allocator**

---

## 41️⃣ What is swap space and how is it used?

A portion of disk used as an **extension of RAM**.
When RAM is full, inactive pages are moved to swap.

---

## 42️⃣ How to monitor memory usage in Linux?

| Command                 | Purpose          |
| :---------------------- | :--------------- |
| `free -h`               | General overview |
| `vmstat`, `top`, `htop` | Live stats       |
| `/proc/meminfo`         | Detailed info    |

---

## 43️⃣ What is the difference between resident and virtual memory?

| Type                        | Description                                              |
| :-------------------------- | :------------------------------------------------------- |
| **RSS (Resident Set Size)** | Actual physical RAM used by the process                  |
| **VSZ (Virtual Size)**      | Total virtual address space, including swapped-out pages |

---

## 44️⃣ What is Non-Uniform Memory Access (NUMA)?

An architecture where memory access time depends on the CPU’s proximity to the memory region.
Linux uses **NUMA-aware scheduling** for performance optimization.

---

## 45️⃣ What are “dirty pages”?

Pages that have been **modified in RAM** but not yet written to disk.
Flushed periodically by the kernel’s write-back daemon.

---

## 46️⃣ What is kernel virtual memory?

The **address space reserved for kernel code**, data, and drivers.
Shared across all processes and protected from user access.

---

## 47️⃣ What are high memory and low memory zones?

* **LowMem**: directly mapped by kernel (typically first 1 GB).
* **HighMem**: requires temporary mapping (used on 32-bit systems).

---

## 48️⃣ What is the purpose of the OOM Killer?

When the system runs out of memory, the **Out-Of-Memory Killer** terminates one or more processes to free memory and prevent system crash.

---

## 49️⃣ What is vmalloc() in kernel?

Allocates **non-contiguous virtual memory** in kernel space — used for large allocations that cannot fit into contiguous physical memory.

---

## 50️⃣ How is memory protection implemented?

Through **page tables** with protection bits (read/write/execute).
The **MMU** enforces these to isolate processes and prevent illegal access.

---

✅ **Summary**

* Virtual memory = abstraction over physical RAM.
* MMU + TLB = hardware that handles translation and caching.
* Page tables = mapping structure per process.
* Paging, swapping, replacement = performance-critical mechanisms.
* Linux uses **buddy system**, **slab allocator**, and **vmalloc** for efficient kernel memory management.

```
