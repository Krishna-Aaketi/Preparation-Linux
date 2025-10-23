# ⚙️ System Calls & Kernel Interface
### Subsystem 5 of 10

---

## 1️⃣ What is a System Call?
A **system call (syscall)** is the **controlled entry point** from user space to kernel space.  
It allows user programs to request kernel-level services such as **I/O, memory management, or process creation**.

---

## 2️⃣ Why Are System Calls Needed?
- User programs run in **user mode** and cannot directly access hardware or protected memory.  
- System calls provide a **safe API** to interact with the kernel — e.g., `read()`, `write()`, `fork()`, etc.

---

## 3️⃣ How Does a System Call Work Internally?
1. Program calls a **C library wrapper** (e.g., `read()`).
2. The wrapper loads the syscall number into a CPU register.
3. A **software interrupt/trap** occurs  
   (`svc #0` on ARM or `syscall` on x86-64), switching the CPU to kernel mode.
4. Kernel executes the corresponding handler from the **syscall table**.
5. Result or error (`errno`) is returned to user space.

### 🧭 How a System Call Works (Step-by-Step)

 1️ **User program** calls `read()`.  
   - Example: `read(fd, buf, count);`

 2️ **C library wrapper** (inside `glibc`) sets up the syscall number and arguments.  
   - It prepares CPU registers with the syscall number and parameters.

 3️ Executes the **syscall instruction** (`syscall` on x86-64 or `svc #0` on ARM).  
   - This triggers a software interrupt (trap) to the kernel.

 4 **CPU switches** from **User Mode → Kernel Mode**.  
   - Privileges change so the kernel can access hardware and memory safely.

 5️ **Kernel** looks up the correct handler in its **system call table**.  
   - Example: `sys_read()` inside the Linux kernel handles the read operation.

 6️ The **kernel executes** the handler and returns a result (or negative error code).  
   - Example: returns number of bytes read, or `-EFAULT`, `-EBADF`, etc.

 7️ The **wrapper converts** the kernel’s return value into a proper C return value.  
   - Negative values are converted to `-1` and `errno` is set.  
   - Your program then receives the result.

---

## 4️⃣ Function Call vs System Call

| Aspect | Function Call | System Call |
|:--|:--|:--|
| Mode | User → User | User → Kernel |
| Overhead | Low | Higher (context switch) |
| Access | User memory only | Can access devices & kernel data |
| Example | `printf()` | `write()` |

---

## 5️⃣ What is Context Switching in System Calls?
When a syscall is invoked, the **CPU switches context** from user mode to kernel mode.  
Registers, stack pointers, and privilege levels change — this transition is called a **mode switch**.

---

## 6️⃣ What Are System Call Numbers?
Each syscall has a unique **numeric ID** (e.g., `__NR_read = 0`).  
The kernel uses this number to **index into the syscall table** and execute the correct handler.

---

## 7️⃣ What is the Syscall Table?
A kernel array mapping syscall numbers to **function pointers** (`sys_read`, `sys_write`, etc.).  
Defined in `syscall_table.S` (architecture-specific).

---

## 8️⃣ What Happens After a Syscall Returns?
- CPU switches back to **user mode**.  
- Kernel writes the **return value or error** into user registers.  
- Execution resumes after the call.

---

## 9️⃣ How Does Linux Report Errors?
System calls return `-1` and set the global variable **`errno`**, accessible via `perror()` or `strerror()`.

---

## 🔟 Common System Call Groups
- **Process Control**: `fork()`, `exec()`, `wait()`, `exit()`  
- **File I/O**: `open()`, `read()`, `write()`, `close()`, `lseek()`  
- **Device I/O**: `ioctl()`  
- **Memory**: `mmap()`, `brk()`, `munmap()`  
- **IPC**: `pipe()`, `shmget()`, `msgsnd()`  
- **Signals**: `kill()`, `sigaction()`  
- **Network**: `socket()`, `bind()`, `send()`, `recv()`

---

## 11️⃣ Library Calls vs System Calls
| Aspect | Library Call | System Call |
|:--|:--|:--|
| Executed Where | User space | Kernel space |
| Implementation | In libc | In kernel |
| Example | `printf()` → uses `write()` | `write()` itself |
| Overhead | Low | High |

---

## 12️⃣ What is a Trap Instruction?
A **CPU instruction** that triggers a **software interrupt**, transferring control to kernel mode — used for syscalls and exceptions.

---

## 13️⃣ What is a Re-entrant System Call?
A syscall that can be safely **interrupted and re-entered** by another thread.  
Example: `getpid()` is re-entrant, `read()` may block.

### 🧩 What is a Re-entrant System Call?

A **re-entrant system call** is a system call that can be **safely interrupted** while it is executing,  
and then **called again (“re-entered”)** by another thread (or signal handler) without causing data corruption or unexpected behavior.

In simple words — it means the system call is **thread-safe** and doesn’t rely on any shared or static data inside the kernel.

---

### 🧠 Explanation

- Re-entrant system calls **don’t modify global kernel data**.
- They **don’t block** waiting for I/O or other resources.
- Two threads (or signals) can execute the same syscall simultaneously, and each will work correctly.

---

### ✅ Example (Re-entrant)

```c
pid_t getpid(void);
```
---
## 16️⃣ sys_enter vs sys_exit Events

They represent **entry and exit points** of system calls — useful for tracing with tools like `strace` and `perf`.

---

## 17️⃣ Tool to Trace Syscalls

`strace` — intercepts and prints all syscalls a process invokes.

---

## 18️⃣ strace vs ltrace

| Tool     | Purpose                                             |
| :------- | :-------------------------------------------------- |
| `strace` | Traces system calls                                 |
| `ltrace` | Traces library calls (e.g., `printf()`, `malloc()`) |

---

## 19️⃣ Blocking vs Non-blocking Syscalls

| Type         | Description                                                       |
| :----------- | :---------------------------------------------------------------- |
| Blocking     | Process sleeps until operation completes (`read()` on empty pipe) |
| Non-blocking | Returns immediately (`EAGAIN`)                                    |

---

## 20️⃣ Asynchronous Syscalls

Execute asynchronously, completing later via **callbacks or signals** (e.g., `aio_read()`, `io_uring`).

---

## 21️⃣ What is io_uring?

A **modern Linux I/O interface** (5.x+) for **asynchronous system calls**.
Uses **ring buffers** shared between kernel and user space — much faster than traditional blocking syscalls.

---

## 22️⃣ System Call Interface (SCI)

The **architecture-specific entry point** that handles transitions from user mode to kernel mode and dispatches syscalls to handlers.

---

## 23️⃣ Where Are Syscalls Implemented?

In kernel subsystems:

* `kernel/`, `fs/`, `mm/`, `ipc/`, `net/`
* Architecture-specific code (e.g., `arch/x86/kernel/syscall_table.S`)

---

## 24️⃣ User Space vs Kernel Space

| Aspect        | User Space           | Kernel Space         |
| :------------ | :------------------- | :------------------- |
| Privileges    | Limited              | Full hardware access |
| Address Range | Low half of memory   | High half            |
| Crash Effect  | Only affects process | May crash system     |

---

### 25️⃣ CPU Privilege Levels (Rings)

Modern CPUs (like x86) define **four privilege levels**, called **Rings**:

| Ring | Privilege Level | Typical Use | Description |
|------|------------------|--------------|--------------|
| **Ring 0** | Highest (most privileged) | **Kernel mode** | Direct access to hardware, memory, CPU instructions. OS kernel, drivers. |
| **Ring 1** | Medium-high | (Unused by most OSes) | Intended for privileged kernel services or device drivers. |
| **Ring 2** | Medium-low | (Unused by most OSes) | Intended for less-privileged drivers or extensions. |
| **Ring 3** | Lowest | **User mode** | User programs and applications. Cannot directly access hardware or memory management. |

---

### 🧭 How it Works

- **User programs** run in **Ring 3** (least privilege).  
- When they need OS services (like file I/O), they make a **system call** → CPU switches to **Ring 0** (kernel mode).  
- The kernel runs the requested service (e.g., `sys_read()`) and then returns to **Ring 3**.

So a syscall moves the CPU **from Ring 3 → Ring 0 → back to Ring 3**.

---

### ⚙️ What about Ring 1 and Ring 2?

- Historically designed for **intermediate privilege layers** (e.g., device drivers or kernel modules).  
- **Modern operating systems (Linux, Windows, macOS)** *don’t use* Rings 1–2 —  
  they only use **Ring 0 (kernel)** and **Ring 3 (user)**.

Why? Simplicity and speed:
- Switching between more rings would complicate context switching.
- Most CPUs now provide **other isolation methods** (e.g., **Virtual Memory**, **IOMMU**, **Supervisor/User bits**, **Hypervisor modes**).

---

### 💡 Modern Usage of Other Privilege Levels

| Layer | CPU Mode / Ring | Used For |
|--------|------------------|----------|
| **User Apps** | Ring 3 | Normal programs |
| **OS Kernel** | Ring 0 | Core operating system |
| **Hypervisor (VMs)** | **“Ring –1”** | Virtual machine monitor (Intel VT-x, AMD-V) |
| **System Management Mode (SMM)** | **“Ring –2”** | Firmware-level operations (BIOS/UEFI) |

---

### 🧠 Summary

- **Rings 1–2** exist in theory but are **not used** in most modern OSes.  
- CPUs *still* reserve them, but **hardware virtualization and protection bits** replaced their purpose.  
- The critical boundary is between **Ring 3 (user)** and **Ring 0 (kernel)**.


---

### 🧩 `copy_from_user()` / `copy_to_user()`

These are **kernel helper functions** used to **safely copy data between user space and kernel space**.

---

### 🧠 Why We Need Them

- **User space** and **kernel space** have separate memory regions for protection.  
- A normal pointer from user space **cannot be trusted** in kernel mode.  
  - The user could pass an invalid address (e.g., `NULL` or an unmapped page).  
  - If the kernel directly dereferenced it → **kernel crash or security bug**.

Hence, the kernel uses **special safe access helpers** to check and copy data properly.

---

### ⚙️ Functions

| Function | Direction | Purpose |
|-----------|------------|----------|
| `copy_from_user(void *to, const void __user *from, unsigned long n)` | User → Kernel | Copies `n` bytes from user-space pointer `from` into kernel buffer `to`. |
| `copy_to_user(void __user *to, const void *from, unsigned long n)` | Kernel → User | Copies `n` bytes from kernel buffer `from` into user-space pointer `to`. |

---

### ✅ Example

```c
ssize_t my_syscall(void __user *user_buf, size_t len) {
    char kbuf[128];

    // Safely copy from user-space buffer
    if (copy_from_user(kbuf, user_buf, len))
        return -EFAULT;   // failed, invalid user address

    // Process data in kernel space...
    process_data(kbuf);

    // Copy result back to user
    if (copy_to_user(user_buf, kbuf, len))
        return -EFAULT;

    return len;  // success
}
```
---

## 27️⃣ Why Can’t Kernel Use User Pointers Directly?

Because user addresses might be **invalid or swapped out**.
Kernel must validate and copy using the above helpers to avoid faults.

---

## 28️⃣ What is a Re-entrant Kernel?

A kernel capable of handling **multiple concurrent syscalls** safely via locks and preemption control.

---

## 29️⃣ Monolithic vs Microkernel Syscalls

| Model                  | Description                                                       |
| :--------------------- | :---------------------------------------------------------------- |
| **Monolithic (Linux)** | Syscalls invoke kernel functions directly within one large kernel |
| **Microkernel**        | Syscalls send messages to user-space servers (slower, modular)    |

---

## 30️⃣ What is a Hypercall?

A syscall-like interface for **guest VMs** to request services from a **hypervisor** (used in virtualization).

---

## 31️⃣ How Many Syscalls in Linux?

Varies by architecture — modern x86-64 supports ~**450–500** syscalls.
See `/usr/include/asm/unistd_64.h`.

---

## 32️⃣ How to List All Syscalls

```bash
ausyscall --dump
grep "__NR_" /usr/include/asm/unistd_64.h
```

---

## 33️⃣ Kernel Thread vs Syscall Execution

| Type          | Runs In              | Description                    |
| :------------ | :------------------- | :----------------------------- |
| Kernel Thread | Kernel mode          | Never returns to user space    |
| Syscall       | User → Kernel → User | Runs on behalf of user process |

---

## 34️⃣ SoftIRQ / Workqueue in Syscalls

Syscalls often **queue asynchronous work** (e.g., disk I/O) to **SoftIRQs** or **workqueues**, allowing deferred execution.

---

## 35️⃣ Role of Scheduler in Syscalls

If a syscall blocks (waiting for I/O), the **scheduler switches** to another runnable process until I/O completes.

---

## 36️⃣ What is a System Call Wrapper?

A **user-space function** (e.g., glibc’s `open()`) that prepares syscall arguments and invokes the real kernel syscall.

---

## 37️⃣ Measuring Time Spent in Syscalls

`strace -T` → shows syscall durations (entry ↔ exit timing).

---

## 38️⃣ Synchronous vs Asynchronous Syscalls

| Type         | Behavior                                                |
| :----------- | :------------------------------------------------------ |
| Synchronous  | Returns only after completion                           |
| Asynchronous | Returns immediately and signals later (`io_uring`, AIO) |

---

## 39️⃣ What is a Fast Path in Syscalls?

Optimized code paths for **frequent syscalls** (like `read()`/`write()`) that skip locks or redundant checks for speed.

---

## 40️⃣ How Does Kernel Protect Against Malicious Inputs?

* Argument validation
* Access checks (UID/GID, capabilities)
* Boundary checking (`copy_from_user`)
* **Seccomp filters** to restrict allowed syscalls

---

## ✅ Summary

* **System calls** are the gateway between user and kernel space.
* Controlled transition via **trap/interrupts** (syscall instruction).
* Managed via **syscall table** and **glibc wrappers**.
* Tools: `strace`, `perf`, `io_uring`.
* Security ensured by **validation**, **privilege levels**, and **Seccomp filters**.

---

```
