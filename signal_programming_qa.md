## Signal Programming – Interview Questions and Answers

### 1. What is a signal in Linux?

**Answer:** A signal is a limited form of inter-process communication used to notify a process that a specific event has occurred.

---

### 2. How do you send a signal to a process?

**Answer:** Using the `kill()` system call:

```c
kill(pid, SIGINT);
```

---

### 3. How do you raise a signal within the same process?

**Answer:** Using the `raise()` function:

```c
raise(SIGTERM);
```

---

### 4. What is the difference between kill() and raise()?

**Answer:**

- `kill()` sends a signal to another process.
- `raise()` sends a signal to the calling process.

---

### 5. What is signal()? Is it reliable?

**Answer:** `signal()` sets a signal handler. It is considered less reliable and portable. Better to use `sigaction()`.

---

### 6. How does sigaction() improve over signal()?

**Answer:**

- Provides more control.
- Can use flags like `SA_SIGINFO`.
- Avoids undefined behavior on reentrancy.

---

### 7. Example of setting up sigaction():

```c
struct sigaction sa;
sa.sa_handler = handler;
sigemptyset(&sa.sa_mask);
sa.sa_flags = 0;
sigaction(SIGINT, &sa, NULL);
```

---

### 8. What is SA\_SIGINFO?

**Answer:** It allows passing additional information to the handler via a `siginfo_t` structure.

---

### 9. What is SA\_RESTART?

**Answer:** It restarts interrupted system calls instead of returning with an error when a signal is caught.

---

### 10. What is sigprocmask()?

**Answer:** It is used to block/unblock signals.

```c
sigset_t set;
sigemptyset(&set);
sigaddset(&set, SIGINT);
sigprocmask(SIG_BLOCK, &set, NULL);
```

---

### 11. What is sigpending()?

**Answer:** It checks for signals that are currently pending (blocked and waiting to be delivered).

---

### 12. What are the default actions of signals?

**Answer:**

- Terminate (e.g., SIGTERM)
- Core dump (e.g., SIGSEGV)
- Stop (e.g., SIGSTOP)
- Continue (e.g., SIGCONT)

---

### 13. Can signal handlers be nested?

**Answer:** Yes, but it's risky. Use `sigaction()` with `SA_NODEFER` if nesting is required.

---

### 14. What are signal-safe functions?

**Answer:** Functions that can be safely called inside a signal handler. Example: `write()`, `kill()`.

---

### 15. How to write a reentrant signal handler?

**Answer:**

- Use only async-signal-safe functions.
- Avoid dynamic memory allocation and non-reentrant functions like `printf()`.

---

### 16. What is a signal mask?

**Answer:** A set of signals whose delivery is currently blocked.

---

### 17. How do you unblock signals?

```c
sigprocmask(SIG_UNBLOCK, &set, NULL);
```

---

### 18. What is sigfillset()?

**Answer:** Initializes a signal set to include all signals.

---

### 19. What is sigemptyset()?

**Answer:** Initializes a signal set to exclude all signals.

---

### 20. What happens if two signals of same type are sent before the first is handled?

**Answer:** Traditional signals are not queued. Only one instance is delivered.

---

### 21. How to handle multiple signals safely?

**Answer:**

- Block signals using `sigprocmask()`.
- Use `sigwait()` or `signalfd()`.

---

### 22. Can signal handlers access global variables?

**Answer:** Yes, but ensure they are accessed in an atomic or thread-safe manner.

---

### 23. What is signalfd()?

**Answer:** Allows signals to be received as file descriptor events, suitable for use with `select()`/`poll()`.

---

### 24. Can signals interrupt system calls?

**Answer:** Yes, unless `SA_RESTART` is used.

---

### 25. What is sigqueue()?

**Answer:** Sends real-time signals with accompanying data.

---

### 26. How to handle SIGCHLD?

**Answer:** Catch it in a handler to reap zombie child processes using `waitpid()`.

---

### 27. What is the purpose of pause()?

**Answer:** Suspends the process until a signal is caught.

---

### 28. What is sleep() interrupted by?

**Answer:** Any signal can interrupt `sleep()` unless `SA_RESTART` is set.

---

### 29. What happens if you call printf() in a signal handler?

**Answer:** It may cause undefined behavior; `printf()` is not async-signal-safe.

---

### 30. How to avoid race conditions with signals?

**Answer:**

- Use `sigprocmask()` to block critical sections.
- Use `volatile sig_atomic_t` for shared flags.

---

### 31. What are real-time signals?

**Answer:** Signals numbered from `SIGRTMIN` to `SIGRTMAX`. They are queued and deliver in order.

---

### 32. Difference between standard and real-time signals?

**Answer:**

- Standard signals are not queued.
- Real-time signals are queued and carry data.

---

### 33. How do you temporarily ignore a signal?

**Answer:** Set the handler to `SIG_IGN`.

```c
signal(SIGINT, SIG_IGN);
```

---

### 34. How do you reset signal handling to default?

**Answer:** Use `SIG_DFL`.

```c
signal(SIGTERM, SIG_DFL);
```

---

### 35. What is sigsuspend()?

**Answer:** Atomically unblocks a signal and suspends the process until that signal arrives.

---

### 36. Can signals be sent between threads?

**Answer:** Yes, using `pthread_kill()`.

---

### 37. What is pthread\_kill()?

**Answer:** Sends a signal to a specific thread.

---

### 38. Are signals inherited across exec?

**Answer:** No, signal handlers are reset to defaults during `exec()`.

---

### 39. What happens to signals during fork()?

**Answer:** The child inherits signal dispositions, but not pending signals.

---

### 40. What is sigwait()?

**Answer:** A synchronous way to wait for signals.

---

### 41. Why prefer sigaction over signal?

**Answer:** `sigaction()` is more reliable, portable, and provides more features.

---

### 42. How to avoid stack corruption in signal handlers?

**Answer:**

- Avoid local non-volatile buffers.
- Keep handler minimal.

---

### 43. What is `volatile sig_atomic_t`?

**Answer:** A data type safe to modify inside signal handlers.

---

### 44. Can we block SIGKILL or SIGSTOP?

**Answer:** No. These signals cannot be caught, blocked, or ignored.

---

### 45. What happens when you send SIGKILL?

**Answer:** The kernel immediately terminates the process; cleanup code doesn't run.

---

### 46. What is SA\_NOCLDWAIT?

**Answer:** Prevents creation of zombie children. SIGCHLD is ignored, and exit status discarded.

---

### 47. What is sigaction.sa\_mask used for?

**Answer:** It specifies signals to block during execution of the handler.

---

### 48. What are async-signal-safe functions?

**Answer:** POSIX specifies functions safe to call from within signal handlers.

---

### 49. How to detect if a signal was delivered?

**Answer:** Use a `volatile sig_atomic_t` flag set in the handler and checked in the main loop.

---

### 50. Why should we avoid dynamic memory allocation in signal handlers?

**Answer:** Allocation functions like `malloc()` are not signal-safe and may cause crashes.

---

### 51. Can signals be prioritized?

**Answer:** Only real-time signals have delivery priority based on number.

---

### 52. What is the use of kill(getpid(), SIGUSR1)?

**Answer:** Sends SIGUSR1 to the current process. Often used for testing handlers.

