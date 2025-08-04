# Qualcomm Interview Questions on Processes (with Answers)

## Section 1: Process Creation and Management

### 1. What is `fork()` in Linux?
**Answer:** `fork()` is a system call used to create a new process by duplicating the calling process. The new process is called the child process.

### 2. How does `fork()` return values?
**Answer:** `fork()` returns:
- `0` to the child process,
- The PID of the child to the parent,
- `-1` on failure.

### 3. What is the difference between `fork()` and `vfork()`?
**Answer:** `vfork()` is similar to `fork()`, but it suspends the parent process until the child exits or calls `exec()`. It is faster and uses less memory.

### 4. What does `exec()` do?
**Answer:** `exec()` replaces the current process image with a new process image (i.e., it loads and runs a new program in the current process).

### 5. What is the use of `wait()`?
**Answer:** `wait()` makes the parent process wait until any of its child processes terminates.

### 6. How is `waitpid()` different from `wait()`?
**Answer:** `waitpid()` allows a parent to wait for a specific child process by providing its PID.

### 7. What is returned by `getpid()`?
**Answer:** Returns the Process ID of the current process.

### 8. What is returned by `getppid()`?
**Answer:** Returns the Parent Process ID of the current process.

### 9. What is the use of `getuid()`?
**Answer:** Returns the real user ID of the calling process.

### 10. What is a zombie process?
**Answer:** A zombie process has completed execution but still has an entry in the process table. It exists because its parent hasn't called `wait()`.

### 11. What is an orphan process?
**Answer:** A child process becomes an orphan when its parent terminates before the child finishes.

### 12. What happens to an orphan process?
**Answer:** The init process (`pid=1`) adopts it.

---

## Section 2: Advanced Process Management

### 13. What is a process image?
**Answer:** It is the memory layout of a process including code, data, heap, and stack segments.

### 14. Describe the layout of a process in memory.
**Answer:** The layout typically includes:
- Text segment (code)
- Data segment (initialized global/static variables)
- BSS segment (uninitialized global/static variables)
- Heap (dynamic memory)
- Stack (function call and local variables)

### 15. What is the difference between parent and child after `fork()`?
**Answer:** They share the same code but have separate address spaces.

### 16. Can a child process become a parent process?
**Answer:** Yes, if it creates its own child using `fork()`.

### 17. What system calls are used to create and execute a new program?
**Answer:** `fork()` and `exec()` family (`execve`, `execl`, `execvp`, etc.)

### 18. What happens if `exec()` fails?
**Answer:** It returns `-1` and sets `errno`.

### 19. What are the different types of `exec()` functions?
**Answer:** `execl()`, `execp()`, `execle()`, `execvp()`, `execv()`.

### 20. What is the role of the `init` process?
**Answer:** It adopts orphaned processes and performs other system initialization tasks.

---

## Section 3: Scenario-based Questions

### 21. How do you avoid zombie processes?
**Answer:** Use `wait()` or `waitpid()` in the parent, or set a signal handler for `SIGCHLD`.

### 22. How to detect zombie processes?
**Answer:** Use `ps aux | grep Z` or look for processes with status `Z`.

### 23. Can you create a daemon process? How?
**Answer:** Yes. Steps:
1. `fork()` and exit parent
2. `setsid()` to start a new session
3. Change working dir to `/`
4. Redirect file descriptors

### 24. How do you communicate between two processes?
**Answer:** Through IPC mechanisms like pipes, message queues, shared memory, or sockets.

### 25. What is a race condition?
**Answer:** It occurs when multiple processes access shared resources without proper synchronization.

### 26. What are signals?
**Answer:** Asynchronous notifications sent to a process to notify it of various events (e.g., `SIGKILL`, `SIGCHLD`).

### 27. How can a process handle signals?
**Answer:** Using the `signal()` or `sigaction()` system calls.

### 28. How do you terminate a process programmatically?
**Answer:** Use `kill(pid, SIGTERM)` or `_exit()`.

### 29. Can you modify the process image during execution?
**Answer:** Yes, using the `exec()` family.

### 30. What is the difference between `exit()` and `_exit()`?
**Answer:** `exit()` performs cleanup (flushes I/O), `_exit()` exits immediately.

---

## Section 4: Miscellaneous

### 31. How can you create multiple child processes?
**Answer:** Call `fork()` in a loop.

### 32. How can you check process status?
**Answer:** Using commands like `ps`, `top`, `htop`, or programmatically with `/proc`.

### 33. What does `kill -9` do?
**Answer:** Sends `SIGKILL` which immediately terminates a process.

### 34. Difference between `SIGTERM` and `SIGKILL`?
**Answer:** `SIGTERM` can be caught and handled; `SIGKILL` cannot be handled or blocked.

### 35. What happens to open file descriptors after fork?
**Answer:** They are copied to the child process (shared reference).

### 36. Can a process block SIGKILL?
**Answer:** No.

### 37. What is `nice` and `renice`?
**Answer:** Used to set and change process priority.

### 38. How does `clone()` differ from `fork()`?
**Answer:** `clone()` allows finer control over what is shared (used in threads).

### 39. How can you avoid memory duplication in fork?
**Answer:** Linux uses Copy-On-Write (COW) to delay actual copying.

### 40. What are threads?
**Answer:** Lightweight processes sharing the same address space.

### 41. What is `waitid()`?
**Answer:** Like `waitpid()`, but gives more detailed info in `siginfo_t`.

### 42. What are process groups?
**Answer:** Set of related processes sharing a group ID.

### 43. What is a session?
**Answer:** Group of process groups created with `setsid()`.

### 44. What happens if you call `fork()` in a multithreaded program?
**Answer:** Only the calling thread is duplicated in the child.

### 45. What is the role of `atexit()`?
**Answer:** Registers functions to be called on normal process termination.

### 46. What is the role of `alarm()`?
**Answer:** Schedules a `SIGALRM` after specified seconds.

### 47. What is `pause()`?
**Answer:** Suspends the process until a signal is received.

### 48. Can a parent kill its child?
**Answer:** Yes, using `kill()`.

### 49. What is `/proc/[pid]/`?
**Answer:** A pseudo-filesystem to access process info.

### 50. Can a child process become a daemon?
**Answer:** Yes, following the daemon creation steps after fork.

---

*Prepared for Qualcomm/Linux system programming interviews.*

