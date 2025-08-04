## Process Management – Interview Questions and Answers

### 1. What is a process?
**Answer:** A process is an instance of a program in execution. It has its own address space, code, data, stack, file descriptors, and execution context.

### 2. What is the use of fork()?
**Answer:** `fork()` creates a new process by duplicating the calling process. The new process is called the child process.

### 3. What is returned by fork()?
**Answer:**
- 0 in the child process
- Child PID in the parent process
- -1 on error

### 4. What does exec() do?
**Answer:** `exec()` replaces the current process image with a new process image (a different program). Variants include `execl()`, `execp()`, `execv()` etc.

### 5. What is wait() used for?
**Answer:** `wait()` suspends the calling process until one of its child processes terminates. It returns the PID of the terminated child.

### 6. What is the difference between wait() and waitpid()?
**Answer:**
- `wait()`: Waits for any child.
- `waitpid(pid, ...)`: Waits for a specific child, and can provide options (e.g., non-blocking).

### 7. What is getpid()?
**Answer:** Returns the process ID of the calling process.

### 8. What is getppid()?
**Answer:** Returns the parent process ID of the calling process.

### 9. What is getuid()?
**Answer:** Returns the real user ID of the calling process.

### 10. What is a zombie process?
**Answer:** A child process that has terminated but still has an entry in the process table (because the parent hasn’t called wait()).

### 11. What is an orphan process?
**Answer:** A child process whose parent has terminated. The init/systemd process typically adopts it.

### 12. What is the process image layout?
**Answer:**
- Text segment: Code
- Data segment: Initialized globals
- BSS: Uninitialized globals
- Heap: Dynamic memory (malloc)
- Stack: Function calls, local variables

### 13. What is the difference between real UID and effective UID?
**Answer:**
- Real UID: ID of the user who started the process
- Effective UID: Used for permission checks

### 14. What are the states of a process?
**Answer:**
- Running
- Runnable
- Blocked (waiting)
- Stopped
- Zombie

### 15. How to avoid zombie processes?
**Answer:**
- Use `wait()` or `waitpid()` in the parent
- Use `SIGCHLD` handler with `SA_NOCLDWAIT`

### 16. What is vfork()?
**Answer:** Similar to `fork()` but child shares the address space with the parent until `exec()` or `_exit()` is called.

### 17. What is the difference between fork() and vfork()?
**Answer:**
- `fork()`: Copies address space (COW)
- `vfork()`: Shares address space, parent is suspended

### 18. What happens when exec() is called after fork()?
**Answer:** The child replaces its memory with a new program, effectively running another executable.

### 19. What is execvp()?
**Answer:** Searches for the executable in PATH and runs it. Commonly used with argument vectors.

### 20. What is the difference between exit() and _exit()?
**Answer:**
- `exit()`: Performs cleanup, flushes stdio buffers
- `_exit()`: Terminates immediately without cleanup

### 21. Can a child process become a zombie if the parent calls _exit()?
**Answer:** Yes, unless the child is reaped by init or handled properly.

### 22. How to handle multiple child processes?
**Answer:** Use `waitpid()` with loops or use `SIGCHLD` signal handlers.

### 23. What is a signal handler?
**Answer:** A function that is called in response to a specific signal (e.g., `SIGCHLD`, `SIGINT`).

### 24. What is execle()?
**Answer:** Similar to `execl()` but allows passing environment variables explicitly.

### 25. What is a daemon process?
**Answer:** A background process detached from terminal, often started during boot (e.g., sshd).

### 26. How do you daemonize a process?
**Answer:**
- `fork()` and exit the parent
- `setsid()`
- Redirect stdio
- `chdir("/")`
- `umask(0)`

### 27. What happens if a child calls fork()?
**Answer:** It creates a grandchild process. All processes continue independently.

### 28. What is the init process?
**Answer:** PID 1. Parent of all orphaned processes. Launches all system services.

### 29. What are the security implications of setuid programs?
**Answer:** Potential vulnerability if input is not properly validated; must drop privileges when not needed.

### 30. How is memory shared between parent and child?
**Answer:** Initially via copy-on-write (COW) after `fork()`. After `exec()`, it is replaced.

### 31. What is the purpose of the wait status?
**Answer:** Encodes how the child exited (normal, signal, etc.) and is inspected using macros like `WIFEXITED()`.

### 32. How to pass data between parent and child?
**Answer:**
- Pipes
- Shared memory
- Sockets
- Files

### 33. What is the stack size limit of a process?
**Answer:** Check using `ulimit -s` or `getrlimit()`.

### 34. What happens if a process exceeds its stack size?
**Answer:** It receives a segmentation fault (`SIGSEGV`).

### 35. What is a process group?
**Answer:** A collection of related processes. Used for job control.

### 36. What is a session?
**Answer:** A collection of process groups. Sessions can have a controlling terminal.

### 37. What is setsid()?
**Answer:** Creates a new session and makes the calling process the session leader.

### 38. How to list all processes?
**Answer:** Use `ps`, `top`, `htop`, or programmatically read `/proc`.

### 39. How to get current process name in C?
**Answer:** Read from `/proc/self/comm` or use `prctl(PR_GET_NAME)`.

### 40. What is the use of nice() and renice()?
**Answer:** Set or change the scheduling priority (niceness) of a process.

### 41. What is the default priority of a Linux process?
**Answer:** Niceness level of 0 (range: -20 to +19).

### 42. What is the PID limit in Linux?
**Answer:** Configurable via `/proc/sys/kernel/pid_max`. Default is typically 32768 or higher.

### 43. What are real-time processes?
**Answer:** Processes with real-time priority (`SCHED_FIFO`, `SCHED_RR`) which are not preempted by normal processes.

### 44. How to trace system calls made by a process?
**Answer:** Use `strace`.

### 45. What is the /proc filesystem?
**Answer:** Virtual filesystem exposing process and system information.

### 46. What is the difference between kill and exit?
**Answer:**
- `kill()`: Sends a signal to a process.
- `exit()`: Terminates the calling process.

### 47. How to terminate a process from code?
**Answer:** Use `exit()` or `kill(getpid(), SIGTERM)`.

### 48. What is the difference between SIGTERM and SIGKILL?
**Answer:**
- `SIGTERM`: Graceful termination
- `SIGKILL`: Immediate, cannot be caught or ignored

### 49. What is ptrace() used for?
**Answer:** Debugging tool that allows inspection and control of other processes.

### 50. What is clone()?
**Answer:** Low-level system call used to create threads and containers. More control than `fork()`.

### 51. What happens if fork() is called in a multi-threaded process?
**Answer:** Only the calling thread is duplicated in the child.

### 52. How to ensure uniqueness of child process IDs?
**Answer:** Kernel ensures unique PIDs. `/proc/sys/kernel/pid_max` defines upper bound.

