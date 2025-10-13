# 🧩 Inter-Process Communication (IPC) -Subsystem 4 of 10

---

## 1️⃣ What is IPC (Inter-Process Communication)?
**IPC** is a mechanism that allows independent processes to **exchange data** and **synchronize activities**, since each process runs in its own address space.

---

## 2️⃣ Why is IPC Needed?
- Share information between processes  
- Coordinate execution order (e.g., producer–consumer models)  
- Avoid race conditions  
- Enable modular and distributed design  

---

## 3️⃣ Main Types of IPC Mechanisms in Linux
- **Pipes** (Anonymous & Named FIFOs)  
- **Message Queues** (SysV & POSIX)  
- **Shared Memory**  
- **Semaphores** (SysV & POSIX)  
- **Sockets** (Local & Network)  
- **Signals**  
- **Memory-Mapped Files (mmap)**  
- **Remote Procedure Calls (RPC)**  

---

## 4️⃣ What is a Pipe?
A **unidirectional communication channel** between two related processes (usually parent and child).  
Created with:
```c
pipe(fd[2]);
````

* `fd[0]` → read end
* `fd[1]` → write end

---

## 5️⃣ Difference Between Anonymous and Named Pipes

| Feature              | Anonymous Pipe     | Named Pipe (FIFO)       |
| :------------------- | :----------------- | :---------------------- |
| Scope                | Parent–child only  | Any processes           |
| Exists in filesystem | No                 | Yes (`/tmp/myfifo`)     |
| Created with         | `pipe()`           | `mkfifo()` or `mknod()` |
| Lifetime             | Until process ends | Until deleted           |

---

## 6️⃣ What is a FIFO?

A **named pipe** that appears as a special file and allows **communication between unrelated processes**.
Opened using `open()`, and used with `read()` and `write()`.

---

## 7️⃣ Why are I/O calls like `read()` and `write()` called blocking?

Because they **pause execution** until data is available (for `read`) or space is free (for `write`) — ensuring synchronization.

---

## 8️⃣ Blocking vs Non-blocking I/O

| Mode         | Behavior                                    |
| :----------- | :------------------------------------------ |
| Blocking     | Waits until operation completes             |
| Non-blocking | Returns immediately (`EAGAIN` if not ready) |

Set non-blocking mode using:

```c
fcntl(fd, F_SETFL, O_NONBLOCK);
```

---

## 9️⃣ I/O Multiplexing Calls

* `select()`
* `poll()`
* `epoll()`
  → Allow a process to **monitor multiple file descriptors** and wake when any become ready.

---

## 🔟 What is a Message Queue?

An IPC mechanism storing **discrete messages** in a queue located in kernel space.
Processes use:

```c
msgsnd();   // send
msgrcv();   // receive
```

Supports **priorities** and **asynchronous communication**.

---

## 11️⃣ Why Use Message Queues Instead of Pipes?

* Structured messages (not byte streams)
* Preserve message boundaries
* Support asynchronous operation
* Allow priorities

---

## 12️⃣ SysV Message Queue System Calls

`msgget()`, `msgsnd()`, `msgrcv()`, `msgctl()`

---

## 13️⃣ What is Shared Memory?

A **region of memory shared** between multiple processes for fast data exchange (no kernel copy).
Created with `shmget()`, attached using `shmat()`.

---

## 14️⃣ Advantages of Shared Memory

✅ Fastest IPC mechanism
✅ Direct memory access
✅ Efficient for large data transfers

---

## 15️⃣ Disadvantages of Shared Memory

❌ Requires explicit synchronization (e.g., semaphores, mutexes)
❌ Risk of data corruption without proper locking

---

## 16️⃣ What is a Semaphore?

A **synchronization primitive** that controls access to shared resources using a counter.
Used via:

* `sem_init()`, `sem_wait()`, `sem_post()` → POSIX
* `semget()` → SysV

---

## 17️⃣ Binary vs Counting Semaphores

| Type     | Range  | Purpose                  |
| :------- | :----- | :----------------------- |
| Binary   | 0 or 1 | Mutual exclusion         |
| Counting | 0 → N  | Resource pool management |

---

## 18️⃣ What is a Mutex?

A **mutual exclusion lock** ensuring only one process/thread accesses a critical section at a time.
Usually built on top of binary semaphores.

---

## 19️⃣ What are Signals in Linux?

**Software interrupts** that notify a process of an event (e.g., `SIGINT`, `SIGTERM`, `SIGKILL`).
Handled using:

```c
signal() or sigaction()
```

---

## 20️⃣ Real-Time Signals

Signals with numbers above `SIGRTMIN`.
Support **queuing** and **carrying extra data**.

---

## 21️⃣ Signal Handling vs Polling

| Method      | Description                                      |
| :---------- | :----------------------------------------------- |
| **Signal**  | Asynchronous event triggered by the kernel       |
| **Polling** | Process repeatedly checks status (CPU-intensive) |

---

## 22️⃣ What is a Socket?

A **bidirectional communication endpoint** between processes — local (UNIX) or network (TCP/IP).
Created using `socket()`, bound using `bind()`, and used with `send()` / `recv()`.

---

## 23️⃣ Types of Sockets

| Type                   | Description                   |
| :--------------------- | :---------------------------- |
| Stream sockets (TCP)   | Reliable, connection-oriented |
| Datagram sockets (UDP) | Message-based, unreliable     |
| Raw sockets            | Direct network access         |
| Unix domain sockets    | Local IPC                     |

---

## 24️⃣ Unix vs Internet Sockets

| Type            | Address Family         | Path/Address    |
| :-------------- | :--------------------- | :-------------- |
| Unix Socket     | `AF_UNIX` / `AF_LOCAL` | Filesystem path |
| Internet Socket | `AF_INET` / `AF_INET6` | IP + Port       |

---

## 25️⃣ What is `select()` Used for?

To **wait for multiple sockets** to become ready for read/write without blocking on one.

---

## 26️⃣ TCP vs UDP Sockets

| Feature     | TCP        | UDP            |
| :---------- | :--------- | :------------- |
| Connection  | Yes        | No             |
| Reliability | Guaranteed | Not guaranteed |
| Order       | Maintained | May reorder    |
| Overhead    | High       | Low            |

---

## 27️⃣ What is a Port Number?

A **16-bit identifier** uniquely identifying a communication endpoint on a host.

---

## 28️⃣ What is IPC Synchronization?

Mechanisms like **locks**, **semaphores**, **monitors** — used to prevent race conditions when accessing shared resources.

---

## 29️⃣ What is Deadlock in IPC?

A state where **two or more processes wait indefinitely** for resources held by each other.

---

## 30️⃣ Conditions for Deadlock

1. Mutual exclusion
2. Hold and wait
3. No preemption
4. Circular wait

---

## 31️⃣ Preventing or Avoiding Deadlocks

* Avoid circular wait (order resource acquisition)
* Use timeouts / try-locks
* Release before requesting new resources
* Apply detection & recovery algorithms

---

## 32️⃣ Barrier Synchronization

All processes/threads **must reach a point** before any proceed.
Used in **parallel computing**.

---

## 33️⃣ SysV vs POSIX IPC

| Aspect         | SysV IPC              | POSIX IPC                      |
| :------------- | :-------------------- | :----------------------------- |
| Identification | Numeric keys (`ftok`) | Named objects (`/sem`, `/shm`) |
| API Style      | Legacy C API          | Modern POSIX API               |
| Lifetime       | Persists after exit   | Removed on close/unlink        |
| Portability    | Limited               | Highly portable                |

---

## 34️⃣ What is `ftok()`?

Generates a **unique IPC key** based on a file path and ID — used to create SysV IPC objects.

---

## 35️⃣ What is `shmctl()`?

Controls shared memory segments — get info, set permissions, or remove the segment.

---

## 36️⃣ What is `semop()`?

Performs operations on **SysV semaphore sets** (increment, decrement, wait).

---

## 37️⃣ What is an IPC Namespace?

Each process can have its **own namespace** so IPC resources (keys, queues, semaphores) are isolated.
👉 Used by **containers** and virtualization systems.

---

## 38️⃣ What is `/proc/sysvipc`?

A virtual directory showing **active SysV IPC objects**: message queues, shared memory segments, semaphores.

---

## 39️⃣ What is Zero-Copy IPC?

A data-sharing technique that avoids kernel copies — e.g., using `mmap()` or shared memory for **direct data access**.

---

## 40️⃣ What is D-Bus?

A **high-level message bus** system used by desktop and system services for IPC — e.g., between GNOME apps and `systemd`.

---

## ✅ Summary

* IPC enables **communication & synchronization** between processes.
* Mechanisms include **pipes**, **message queues**, **shared memory**, **sockets**, **signals**, and **semaphores**.
* **Shared memory** is fastest; **sockets** support networking; **signals** are event-driven.
* Proper **synchronization** avoids race conditions and deadlocks.

---

```
