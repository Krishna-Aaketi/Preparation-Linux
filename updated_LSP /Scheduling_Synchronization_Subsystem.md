## ⚡ SCHEDULING & SYNCHRONIZATION

### (Subsystem 6 of 10)

---

### **1. What is CPU scheduling?**

CPU scheduling is the kernel’s decision process that selects **which ready process/thread runs next** on a CPU core to maximize performance and responsiveness.

---

### **2. What are the goals of scheduling?**

* Fair CPU distribution among processes
* Maximum throughput
* Minimum response and waiting time
* Support for priorities and deadlines
* Balanced CPU utilization

---

### **3. What are the main types of schedulers?**

1. **Long-term (Job) scheduler** – decides which jobs enter memory.
2. **Short-term (CPU) scheduler** – picks the next ready process for CPU.
3. **Medium-term scheduler** – swaps processes in/out of memory.

---

### **4. What is pre-emptive vs non-pre-emptive scheduling?**

| Type                | Behavior                                                      |
| ------------------- | ------------------------------------------------------------- |
| **Pre-emptive**     | CPU can be taken away from a running process (Linux default). |
| **Non-pre-emptive** | Process keeps CPU until it yields or blocks.                  |

---

### **5. What are common scheduling algorithms?**

* **FCFS** (First Come First Serve)
* **SJF** (Shortest Job First)
* **RR** (Round Robin)
* **Priority Scheduling**
* **Multilevel Queue**
* **Completely Fair Scheduler (CFS)** – default in Linux.

---

### **6. What is the Linux Completely Fair Scheduler (CFS)?**

CFS models CPU time as a **virtual timeline** and aims to give each runnable process a fair share based on its weight (priority).
It uses a **red-black tree** sorted by virtual runtime (`vruntime`).

---

### **7. What are Linux scheduling classes?**

1. **SCHED_OTHER** – normal time-sharing (CFS)
2. **SCHED_BATCH** – for background non-interactive jobs
3. **SCHED_IDLE** – lowest-priority tasks
4. **SCHED_FIFO** – real-time, first-in-first-out
5. **SCHED_RR** – real-time, round-robin
6. **SCHED_DEADLINE** – for time-critical tasks with deadlines

---

### **8. What is a scheduling policy?**

It defines how processes within a class are chosen for CPU time (e.g., FIFO, RR, or fair).

---

### **9. What is a time quantum (timeslice)?**

The maximum amount of CPU time a process can run before being pre-empted by the scheduler.

---

### **10. What is process priority and nice value?**

* **Priority:** internal numeric rank used by scheduler.
* **Nice value (–20 to +19):** user-level weight; lower = higher priority.

---

### **11. What is real-time scheduling?**

Scheduling where deadlines are strict and tasks must finish on time. Linux uses **POSIX real-time classes (FIFO, RR, DEADLINE)** for such workloads.

---

### **12. What is load balancing in multiprocessor systems?**

Kernel moves tasks among CPU cores to keep workloads evenly distributed and avoid idle CPUs.

---

### **13. What are soft vs hard real-time tasks?**

| Type        | Guarantee                                   |
| ----------- | ------------------------------------------- |
| **Hard RT** | Must meet every deadline (e.g., avionics).  |
| **Soft RT** | Occasional misses acceptable (e.g., audio). |

---

### **14. What is the difference between process and thread scheduling?**

Both use same kernel scheduler; threads of a process may run on different CPUs concurrently, sharing memory.

---

### **15. What is context switch overhead?**

The CPU time spent saving/restoring registers, memory mappings, and kernel state when switching tasks.

---

### **16. What is priority inversion?**

When a high-priority task waits for a low-priority one holding a needed lock.
Prevented by **priority inheritance** mechanisms.

---

### **17. What is CPU affinity?**

Binding a process or thread to specific CPU cores to improve cache locality (`taskset`, `sched_setaffinity()`).

---

### **18. What is a runqueue?**

Per-CPU data structure containing all runnable tasks for that core, sorted by policy and priority.

---

### **19. What is the role of `sched_yield()`?**

Voluntarily yields CPU, letting another equal-priority thread run.

---

### **20. How does the Linux kernel handle sleeping vs runnable tasks?**

* **Runnable:** kept in runqueue.
* **Sleeping/blocked:** removed and woken when event completes.

---

### **21. What are kernel synchronization primitives?**

Mechanisms that coordinate concurrent access to shared data:
**spinlocks, mutexes, semaphores, rwlocks, seqlocks, completions, RCU.**

---

### **22. What is a critical section?**

Code that accesses shared resources and must not be executed by multiple threads simultaneously.

---

### **23. What is a race condition?**

Unpredictable behavior when multiple threads modify shared data concurrently without synchronization.

---

### **24. What is a spinlock?**

A lock where threads **busy-wait (spin)** in a loop until the lock becomes available.
Used in kernel for short critical sections (non-sleepable contexts).

---

### **25. What is a mutex?**

A **sleeping lock**: if not available, the thread sleeps instead of spinning.
Used in user space (`pthread_mutex_t`) or kernel (`struct mutex`).

---

### **26. What is the difference between spinlock and mutex?**

| Feature  | Spinlock                  | Mutex           |
| -------- | ------------------------- | --------------- |
| Waiting  | Busy-wait                 | Sleeps          |
| Context  | Non-sleep (interrupt)     | Process context |
| Overhead | Low                       | Higher          |
| Use when | Critical section < few µs | Longer sections |

---

### **27. What is a semaphore (kernel)?**

A counter-based lock for controlling access to a finite number of resources (`down()`, `up()` in kernel).

---

### **28. What is a reader-writer (RW) lock?**

Allows multiple concurrent readers or one exclusive writer — increases parallelism for read-mostly data.

---

### **29. What is a completion variable?**

A kernel sync primitive used to signal task completion (`wait_for_completion()` / `complete()`).

---

### **30. What is RCU (Read-Copy-Update)?**

A lock-free synchronization method for read-mostly data: readers access old copies while writers update safely in parallel.

---

### **31. What is atomic operation?**

An operation that executes entirely or not at all, without interruption (e.g., `atomic_inc()`); prevents race conditions without full locks.

---

### **32. What are memory barriers?**

Instructions that enforce ordering of memory operations on multiprocessors (`smp_mb()`, `rmb()`, `wmb()`).

---

### **33. What is kernel pre-emption?**

Allows higher-priority tasks or interrupts to pre-empt running kernel code for better latency.

---

### **34. What are softirqs and tasklets?**

Deferred-execution mechanisms used for interrupt bottom halves; run with pre-emption disabled but can be scheduled on CPUs.

---

### **35. What is a workqueue?**

Kernel facility that defers work to kernel threads (`kworker`) that can sleep — useful for long post-interrupt tasks.

---

### **36. What is interrupt context vs process context?**

* **Interrupt context:** code runs with interrupts disabled, can’t sleep.
* **Process context:** code runs on behalf of a process, can block.

---

### **37. What are lockdep and spinlock debugging?**

Kernel features that detect deadlocks and improper lock usage during development (`CONFIG_LOCKDEP`).

---

### **38. What is a deadlock in synchronization?**

When two or more threads hold locks and wait indefinitely for each other — system hangs.

---

### **39. How to prevent deadlocks?**

* Always lock in consistent order.
* Use try-locks/timeouts.
* Break cycles by releasing unneeded locks early.
* Keep critical sections short.

---

### **40. What is seqlock?**

A lock type using a sequence counter: writers increment counter; readers retry if it changes — efficient for infrequent writes.

---

### **41. What is the difference between kernel spinlocks and user-space pthread locks?**

Kernel locks disable pre-emption/interrupts and work inside kernel; pthread locks rely on scheduler and futex system calls.

---

### **42. What is a futex (Fast Userspace Mutex)?**

User-space locking primitive backed by the kernel only on contention, minimizing syscall overhead.

---

### **43. What is scheduling latency?**

The delay between a task becoming runnable and actually being scheduled — minimized by pre-emption and real-time policies.

---

### **44. What is load average in Linux?**

Average number of runnable or uninterruptible tasks over 1, 5, 15 minutes; shown by `uptime` or `/proc/loadavg`.

---

### **45. What is NUMA-aware scheduling?**

Scheduler prefers CPUs and memory from the same NUMA node to improve cache/memory locality.

---

### **46. What is kernel tick (timer interrupt)?**

Periodic interrupt (HZ times per sec) used by scheduler for accounting and pre-emption; modern kernels support **tickless scheduling** (`CONFIG_NO_HZ`).

---

### **47. What is soft vs hard CPU affinity?**

* **Soft affinity:** scheduler tries to keep a process on the same core.
* **Hard affinity:** process is restricted to specific cores only.

---

### **48. What is a scheduler entity?**

Kernel data structure (`sched_entity`) representing a task within the scheduling tree, storing vruntime, weight, and priority.

---

### **49. What is a scheduling domain?**

Hierarchy grouping CPUs for load balancing and topology awareness (core → package → NUMA node).

---

### **50. What is priority inheritance protocol?**

Temporarily boosts a low-priority task’s priority when it holds a lock needed by a higher-priority task — solves priority inversion.

---
