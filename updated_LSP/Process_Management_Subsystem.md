# 🧠 Process Management (Subsystem 1 of 10)

---

## 1️⃣ What is a process?
A **process** is an executing instance of a program, with its own memory, registers, and system resources managed by the OS kernel.

---

## 2️⃣ What is a PCB (Process Control Block)?
The **PCB** is a data structure maintained by the kernel for every process.  
It contains:

- PID (Process ID)  
- Process state (Running, Ready, Waiting, etc.)  
- CPU registers and program counter  
- Scheduling info (priority, timeslices)  
- Memory mappings (page tables)  
- File descriptor table  
- Accounting info (CPU usage, runtime, etc.)

---

## 3️⃣ What are process states in Linux?
- **Running (R)** – executing or ready to execute  
- **Sleeping (S)** – waiting for an event or I/O  
- **Disk Sleep (D)** – uninterruptible sleep  
- **Stopped (T)** – paused (e.g., by SIGSTOP)  
- **Zombie (Z)** – finished but not yet reaped  
- **Dead (X)** – completely terminated  

---

## 4️⃣ What is the difference between fork() and exec()?

| Aspect | fork() | exec() |
|:--|:--|:--|
| Purpose | Creates new process (child) | Replaces current process |
| Memory | Child gets copy of parent memory | Old memory replaced |
| PID | New PID assigned | Same PID retained |
| Return | Returns twice (0 to child, PID to parent) | Returns only on failure |

---

## 5️⃣ What does wait() do?
`wait()` allows a parent process to:
- Wait for a child to finish execution  
- Collect the child’s exit status  
- Prevent zombie processes  

It **blocks** the parent until a child terminates.

---

## 6️⃣ What is vfork()?
`vfork()` creates a child process sharing the parent’s memory temporarily until the child calls `exec()` or `_exit()`.  
⚠️ It’s faster but risky — child must not modify parent’s memory before exec.

---

## 7️⃣ What is a zombie process?
A process that has finished execution but still has an entry in the process table because its parent hasn’t read its exit status via `wait()`.

---

## 8️⃣ What is an orphan process?
A process whose parent has terminated before it.  
It gets adopted by **init/systemd (PID 1)**.

---

## 9️⃣ Difference between a process and a thread

| Feature | Process | Thread |
|:--|:--|:--|
| Memory | Independent memory space | Shared memory space |
| PID | Unique PID | Same PID (different TID) |
| Overhead | Heavy | Lightweight |
| Communication | IPC needed | Shared variables |

---

## 🔟 What is the use of getpid() and getppid()?
- `getpid()` → returns current process ID  
- `getppid()` → returns parent process ID  

---

## 11️⃣ What is the purpose of the clone() system call?
`clone()` is a low-level syscall used to create processes or threads with fine-grained control over what is shared (memory, file descriptors, signal handlers, etc.).  
🧩 Used internally by `pthread_create()`.

---

## 12️⃣ What is exit() vs _exit() difference?
- `exit()` → graceful termination (flushes I/O, runs atexit handlers)  
- `_exit()` → immediate termination (used by child after fork())

---

## 13️⃣ What is a daemon process?
A **background process** detached from any terminal, usually started at boot (e.g., `sshd`, `cron`).  
Runs continuously to provide system services.

---

## 14️⃣ What are process groups and sessions?
- **Process group**: collection of processes that can receive signals together (e.g., Ctrl+C).  
- **Session**: group of process groups with one controlling terminal (used for job control).

---

## 15️⃣ What is nice() and renice()?
These adjust **process scheduling priority**.

Range: `-20` (highest) to `+19` (lowest)  
- `nice()` sets priority at start.  
- `renice()` modifies running processes.

---

## 16️⃣ What are the three scheduling classes in Linux?
- **SCHED_OTHER** – normal timesharing  
- **SCHED_FIFO** – real-time, first-in-first-out  
- **SCHED_RR** – real-time, round-robin  

---

## 17️⃣ What is context switching?
The process of saving the CPU state of one process and loading another’s state so the CPU can switch between them.

---

## 18️⃣ What is the role of the scheduler?
The **scheduler** decides which process gets the CPU next based on priority, fairness, and policy.

---

## 19️⃣ What are the main types of schedulers?
- **Long-term scheduler**: admits new jobs (rare in Linux)  
- **Short-term scheduler**: selects which ready process runs next  
- **Medium-term scheduler**: handles swapping in/out processes  

---

## 20️⃣ What is the difference between user and kernel threads?
- **User threads**: managed in user space (fast but not preemptive)  
- **Kernel threads**: managed by kernel (fully preemptive, visible to OS)

---

## 21️⃣ How does Linux identify processes?
Each process has:
- **PID** → process ID  
- **PPID** → parent process ID  
- **TGID** → thread group ID (for multi-threaded processes)

---

## 22️⃣ What is a process hierarchy?
All processes form a **tree structure**, starting from **init (PID 1)**.  
Each process has exactly one parent and may have multiple children.

---

## 23️⃣ What is the purpose of chroot()?
`chroot()` changes the root directory (`/`) for the current process — creating a sandbox or isolated environment.

---

## 24️⃣ What is setuid() and setgid() used for?
Used to **change user or group ID** of a process to gain or drop privileges safely.  
📘 Example: `passwd` uses setuid() to run as root even for normal users.

---

## 25️⃣ How do you create and run a daemon manually?
Steps:
1. `fork()` → parent exits  
2. `setsid()` → become session leader  
3. `chdir("/")` → detach from any directory  
4. `umask(0)` → reset permissions  
5. Close stdin, stdout, stderr  

---

## 26️⃣ What is the ps command used for?
Lists process details such as **PID, state, CPU usage, memory usage**.

---

## 27️⃣ What is top and htop used for?
Displays **real-time process information** and **CPU/memory utilization**.

---

## 28️⃣ What is the difference between user time and system time?
- **User time** → executing user-level code  
- **System time** → executing kernel-level code on behalf of the process  

---

## 29️⃣ What is a signal in process management?
A software interrupt used to notify a process about an event (e.g., `SIGINT`, `SIGKILL`, `SIGSTOP`).

---

## 30️⃣ How does kill work?
`kill(pid, sig)` sends a signal to a process.  
Example:
```bash
kill -9 <PID>   # sends SIGKILL
````

---

## 31️⃣ What is a signal handler?

A function registered with `signal()` or `sigaction()` to define custom behavior when a signal arrives.

---

## 32️⃣ What is process priority vs nice value?

* **Priority** → kernel-internal value
* **Nice value** → user-visible adjustment
  Lower nice → higher priority.

---

## 33️⃣ What is ps -ef vs ps aux difference?

| Command  | Style          | Description                           |
| :------- | :------------- | :------------------------------------ |
| `ps -ef` | System V style | Traditional UNIX format               |
| `ps aux` | BSD style      | Displays memory and CPU in BSD format |

---

## 34️⃣ What is interleaving in processes?

When CPU time is shared among processes so they appear to run simultaneously (on a single-core system).

---

## 35️⃣ What is preemption?

When the scheduler forcibly suspends a process so another can run — foundation of **multitasking**.

---

## 36️⃣ What is time slicing?

Allocating a small **CPU quantum** (e.g., 10ms) to each runnable process cyclically.

---

## 37️⃣ What is the difference between kernel threads and user processes?

* **Kernel threads** → run entirely in kernel mode (background tasks like flushing buffers).
* **User processes** → execute user code and enter kernel mode via syscalls.

---

## 38️⃣ What is /proc/[pid]/?

A **virtual directory** exposing process details like memory maps, cmdline, open files, etc.

---

## 39️⃣ What is the difference between kill, killall, and pkill?

| Command   | Target  | Description                    |
| :-------- | :------ | :----------------------------- |
| `kill`    | PID     | Send signal by process ID      |
| `killall` | Name    | Send signal by process name    |
| `pkill`   | Pattern | Match by name or regex pattern |

---

## 40️⃣ What is a job in Linux?

A **job** is a process started by a shell, often in the background (`&`).
Managed using **job control** commands:

```bash
jobs
fg %1
bg %2
```

---

✅ **Summary**

* Process = running program instance
* PCB = metadata for each process
* fork(), exec(), wait(), clone() = process creation mechanisms
* Scheduler = CPU time allocator
* Signals = event communication mechanism
* Tools: `ps`, `top`, `kill`, `/proc/[pid]/`

```

Would you like me to generate this as a downloadable **`process_management.md`** file for your Linux notes repository?
```
