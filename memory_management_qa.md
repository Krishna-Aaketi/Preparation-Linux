## Memory Management – Interview Questions and Answers

### Dynamic Memory Allocation

**1. What is the difference between malloc(), calloc(), and realloc()?**

- `malloc(size)`: Allocates uninitialized memory of given size.
- `calloc(n, size)`: Allocates memory for an array of `n` elements and initializes to zero.
- `realloc(ptr, new_size)`: Resizes the previously allocated memory.

**2. What happens if you don’t free memory?**\
Memory leaks occur, leading to eventual exhaustion of memory.

**3. Can you call free() on NULL pointer?**\
Yes, it's safe. `free(NULL)` does nothing.

**4. What does realloc(NULL, size) do?**\
Equivalent to `malloc(size)`.

**5. What happens if realloc() fails?**\
It returns NULL and original memory remains valid.

**6. Can malloc() return NULL?**\
Yes, if memory cannot be allocated.

**7. How do you detect memory leaks?**\
Using tools like `valgrind`, `asan`, or custom logic.

**8. What is a double free error?**\
Calling `free()` on the same pointer twice, causing undefined behavior.

**9. What is a dangling pointer?**\
A pointer pointing to freed or invalid memory.

**10. How is malloc() implemented in Linux?**\
It internally uses `brk()` and `mmap()`.

### mmap()

**11. What is mmap()?**\
Maps files or devices into memory.

```c
void* addr = mmap(NULL, size, PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
```

**12. What are typical use cases for mmap()?**\
File I/O, shared memory, memory-mapped devices.

**13. What is the difference between MAP\_SHARED and MAP\_PRIVATE?**

- `MAP_SHARED`: Writes are visible to others and backed by file.
- `MAP_PRIVATE`: Changes are not visible to others and not saved.

**14. What does munmap() do?**\
Unmaps a previously mapped memory region.

```c
munmap(addr, size);
```

**15. What is mprotect()?**\
Changes protection of memory pages (e.g., make read-only).

```c
mprotect(addr, size, PROT_READ);
```

**16. What is memory-mapped file I/O?**\
Accessing file contents directly in memory via `mmap()`.

**17. Can you mmap() anonymous memory?**\
Yes, using `MAP_ANONYMOUS` flag.

**18. How do you sync changes back to disk?**\
Using `msync()` function.

**19. What happens if you access memory beyond mmap size?**\
Segmentation fault.

**20. How is mmap() different from malloc()?**\
`malloc()` allocates from heap, `mmap()` maps memory regions or files directly.

### brk(), sbrk()

**21. What is brk()?**\
Sets the end of the data segment (heap).

**22. What is sbrk()?**\
Increments the program’s data segment.

```c
void* old_brk = sbrk(0);
sbrk(increment);
```

**23. Why are brk()/sbrk() deprecated in favor of mmap()?**\
Less flexible and can conflict with stack growth. `mmap()` is safer and more scalable.

**24. What is the heap?**\
A region of a process's memory used for dynamic memory allocation.

**25. What is the stack?**\
Memory used for function call frames and local variables.

### Miscellaneous

**26. What is memory fragmentation?**\
Unused memory holes created by frequent allocations and deallocations.

**27. What is the difference between internal and external fragmentation?**

- Internal: Wasted space within allocated blocks.
- External: Wasted space between allocated blocks.

**28. What is page fault?**\
Occurs when accessing a page not currently in physical memory.

**29. What is a segmentation fault?**\
Illegal access of memory (e.g., dereferencing invalid pointer).

**30. What is virtual memory?**\
An abstraction allowing processes to use more memory than physically available.

**31. How does the kernel allocate memory to user space?**\
Via system calls like `brk()` and `mmap()`.

**32. What is copy-on-write?**\
Mechanism where memory is duplicated only when modified (used in `fork()`).

**33. How does malloc() know the size to free()?**\
Allocators maintain metadata with each allocation.

**34. What is heap corruption?**\
Overwriting heap metadata causing undefined behavior.

**35. What is the maximum size allocatable with malloc()?**\
Depends on system architecture and available memory.

**36. What are guard pages?**\
Memory pages with no access to catch stack overflows.

**37. What is memory alignment?**\
Ensures data structures are stored on boundaries suitable for performance and hardware requirements.

**38. How to check memory usage of a process?**\
Using `top`, `ps`, `/proc/<pid>/status`, or `pmap`.

**39. What are page size and frame size?**\
Typically 4KB on x86\_64. Page size is the basic unit of memory management.

**40. How to allocate aligned memory in C?**\
Using `posix_memalign()`.

**41. What is NUMA?**\
Non-Uniform Memory Access – memory access time depends on memory location relative to processor.

**42. How does lazy allocation work?**\
Memory is not physically allocated until it is accessed.

**43. What is overcommit memory?**\
Kernel allows allocating more memory than physically available based on heuristics.

**44. What is RSS and VMS?**

- RSS: Resident Set Size (physical memory used)
- VMS: Virtual Memory Size (total address space)

**45. How to increase stack size?**\
Using `ulimit -s` or `pthread_attr_setstacksize()`.

**46. What is the default heap size?**\
Depends on system but usually limited by available virtual memory.

**47. What happens if memory is not page aligned?**\
May cause performance degradation or access errors depending on the system.

**48. What is kernel memory vs user memory?**\
User memory is accessible by applications. Kernel memory is reserved for OS.

**49. What is memory mapping of ELF binary?**\
The process image includes text, data, heap, stack – loaded via ELF segments.

**50. How to debug memory issues in C programs?**\
Using `valgrind`, `gdb`, `asan`, and logging malloc/free activities.

---

This document covers core and advanced questions for Memory Management in Linux system programming interviews.

