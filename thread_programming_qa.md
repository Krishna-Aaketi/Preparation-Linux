## Thread Programming – Interview Questions and Answers

### 1. What is a thread?

**Answer:** A thread is the smallest unit of CPU execution within a process. Threads within the same process share the same address space but have separate stacks and registers.

### 2. What is the difference between a thread and a process?

**Answer:**

- Process: Independent execution unit with its own address space.
- Thread: Lightweight unit within a process, shares memory but has its own stack and registers.

### 3. How do you create a thread in C using POSIX APIs?

**Answer:** Use `pthread_create()`.

```c
pthread_t tid;
pthread_create(&tid, NULL, thread_func, NULL);
```

### 4. What is pthread\_join() used for?

**Answer:** It waits for the specified thread to terminate.

```c
pthread_join(tid, NULL);
```

### 5. What happens if you don't call pthread\_join()?

**Answer:** The thread may remain in a zombie state; resources may not be released.

### 6. What is pthread\_exit()?

**Answer:** It terminates the calling thread and optionally returns a value to pthread\_join().

### 7. How can a thread return a value?

**Answer:** By passing a pointer to the value in `pthread_exit()` and retrieving it in `pthread_join()`.

### 8. What are detached threads?

**Answer:** Threads that clean up their resources automatically upon termination using `pthread_detach()`.

### 9. How do you detach a thread?

**Answer:**

```c
pthread_detach(pthread_self());
```

### 10. What is the default state of a thread: joinable or detached?

**Answer:** Joinable.

### 11. What is a race condition?

**Answer:** A condition where the outcome depends on the sequence or timing of threads accessing shared resources.

### 12. What are mutexes?

**Answer:** Mutexes (mutual exclusion) are used to prevent concurrent access to shared resources.

### 13. How do you use a pthread mutex?

**Answer:**

```c
pthread_mutex_t lock;
pthread_mutex_init(&lock, NULL);
pthread_mutex_lock(&lock);
// critical section
pthread_mutex_unlock(&lock);
```

### 14. What happens if a thread tries to lock an already locked mutex?

**Answer:** The thread is blocked until the mutex becomes available.

### 15. What is a deadlock?

**Answer:** A condition where two or more threads are waiting for each other to release resources, and none proceed.

### 16. How to avoid deadlocks?

**Answer:**

- Acquire locks in a consistent order
- Use timeout-based locks
- Avoid holding multiple locks

### 17. What is a condition variable?

**Answer:** A synchronization primitive to wait for certain conditions in multithreaded programs.

### 18. How to use a pthread condition variable?

**Answer:**

```c
pthread_cond_t cond;
pthread_cond_init(&cond, NULL);
pthread_cond_wait(&cond, &mutex);
pthread_cond_signal(&cond);
```

### 19. What is the use of pthread\_cond\_wait()?

**Answer:** It blocks the thread until a signal is received. The mutex is unlocked during the wait.

### 20. What is pthread\_cond\_signal()?

**Answer:** It wakes one of the threads waiting on the condition variable.

### 21. What is pthread\_cond\_broadcast()?

**Answer:** Wakes all threads waiting on the condition variable.

### 22. What is thread-safe code?

**Answer:** Code that functions correctly during simultaneous execution by multiple threads.

### 23. How do you ensure thread-safety?

**Answer:** By using synchronization mechanisms like mutexes, atomic operations, or designing stateless/reentrant functions.

### 24. What is a reentrant function?

**Answer:** A function that can be safely interrupted and called again before the previous call completes.

### 25. What is the difference between thread-safe and reentrant?

**Answer:**

- Thread-safe: Safe in multithreaded context.
- Reentrant: Safe to call again even if interrupted (doesn't use shared static data).

### 26. What are thread attributes?

**Answer:** Properties like stack size, scheduling policy, and detach state defined using `pthread_attr_t`.

### 27. How do you set thread stack size?

**Answer:**

```c
pthread_attr_t attr;
pthread_attr_init(&attr);
pthread_attr_setstacksize(&attr, size);
```

### 28. What is the default thread stack size?

**Answer:** Typically 8 MB on Linux.

### 29. What is the lifetime of a thread?

**Answer:** From creation via `pthread_create()` until exit via `pthread_exit()` or return from the thread function.

### 30. Can threads share file descriptors?

**Answer:** Yes, since they share the process address space and resources.

### 31. Can signal handlers be shared across threads?

**Answer:** Yes, but signals are delivered to one thread, usually the main or any thread not blocking the signal.

### 32. What is pthread\_self()?

**Answer:** Returns the thread ID of the calling thread.

### 33. What is pthread\_equal()?

**Answer:** Compares two thread IDs.

### 34. How do you cancel a thread?

**Answer:** Use `pthread_cancel(thread_id);` and handle it with `pthread_setcancelstate()`.

### 35. How to handle cancellation cleanly?

**Answer:** Use cancellation points and cleanup handlers via `pthread_cleanup_push()`.

### 36. What are cancellation points?

**Answer:** Places where thread checks if it should terminate (e.g., `read()`, `sleep()`, `pthread_cond_wait()`).

### 37. What is a spinlock?

**Answer:** A busy-wait lock where the thread continuously checks until the lock is acquired. Suitable for short waits.

### 38. What is a read-write lock?

**Answer:** Allows multiple readers or one writer. Writers get exclusive access.

### 39. What are barriers in threading?

**Answer:** Synchronization point where threads wait until all threads reach that point.

### 40. What is the difference between mutex and semaphore?

**Answer:**

- Mutex: Lock owned by one thread.
- Semaphore: Counter for resource availability, not thread-specific.

### 41. When should you use a semaphore over a mutex?

**Answer:** When multiple instances of a resource need to be managed.

### 42. What is false sharing?

**Answer:** Performance degradation when threads on different cores modify variables on the same cache line.

### 43. What are thread-local variables?

**Answer:** Variables local to a thread using `__thread` or `pthread_key_t` in C.

### 44. What is the effect of thread priority?

**Answer:** It influences scheduling but may be ignored unless using real-time scheduling policies.

### 45. How to set thread scheduling policy?

**Answer:** Use `pthread_attr_setschedpolicy()`.

### 46. What are the types of scheduling policies?

**Answer:** `SCHED_OTHER`, `SCHED_FIFO`, `SCHED_RR`.

### 47. What is thread affinity?

**Answer:** Binding a thread to a specific CPU core using `pthread_setaffinity_np()`.

### 48. Can a thread modify another thread's data?

**Answer:** Yes, if the memory is shared. Proper synchronization must be used.

### 49. What is the effect of fork() in multithreaded programs?

**Answer:** Only the calling thread is duplicated in the child. Others are not.

### 50. Is printf() thread-safe?

**Answer:** On most systems, yes, but it may block or serialize output. Use thread-safe logs in real-time systems.

### 51. What libraries are required to compile pthread code?

**Answer:** Link with `-lpthread` or `-pthread`.

### 52. What is the difference between `-lpthread` and `-pthread`?

**Answer:** `-pthread` also defines `_REENTRANT` and sets necessary flags. Preferred option.

### 53. What are some common threading pitfalls?

**Answer:**

- Data races
- Deadlocks
- Resource leaks
- False sharing
- Overhead from excessive locking

