# 💾 File System & I/O Management (Subsystem 3 of 10)

---

## 1️⃣ What is a File System?
A **file system** organizes and manages how data is stored and retrieved on storage devices.  
It defines the **layout of files**, **directories**, **permissions**, and **metadata**.

---

## 2️⃣ What is the Virtual File System (VFS)?
The **VFS** is a kernel abstraction layer that provides a **uniform interface** to multiple file-system types (e.g., ext4, FAT, NTFS, NFS).  
It hides hardware and implementation details from user programs.

---

## 3️⃣ Main Types of Files in Linux
| Type | Description |
|:--|:--|
| `-` | Regular files |
| `d` | Directory files |
| `l` | Symbolic links |
| `c` | Character device files |
| `b` | Block device files |
| `s` | Socket files |
| `p` | Named pipes / FIFOs |

---

## 4️⃣ What is an inode?
An **inode (index node)** is a kernel data structure storing metadata about a file (not its name or contents).  
Contains:
- File type and permissions  
- Owner UID/GID  
- Timestamps (access, modify, change)  
- File size  
- Link count  
- Block pointers  

---

## 5️⃣ Where is file information stored?
File metadata is stored inside the **inode object** — both on disk and cached in memory.

---

## 6️⃣ What is a directory entry (dentry)?
A **dentry** maps a filename to its inode number.  
Directories contain dentries that associate **names → inodes**.

---

## 7️⃣ What happens when `open()` is called?
1. Kernel locates the directory entry → inode.  
2. Creates a **file object** (`struct file`).  
3. Stores its address in the process’s **File Descriptor Table**.  
4. Returns a **file descriptor (fd)** to user space.

---

## 8️⃣ What is a file descriptor?
A small non-negative integer indexing an entry in a process’s **File Descriptor Table** — used by `read()`, `write()`, `lseek()`, etc.

---

## 9️⃣ What is stdin, stdout, and stderr?
| FD | Name | Description |
|:--|:--|:--|
| 0 | stdin | Standard input |
| 1 | stdout | Standard output |
| 2 | stderr | Standard error |

---

## 🔟 Basic File I/O System Calls
`open()`, `read()`, `write()`, `lseek()`, `close()` —  
called **universal I/O** because they work on files, devices, sockets, and pipes alike.

---

## 11️⃣ Buffered vs Unbuffered I/O
| Type | Calls | Behavior |
|:--|:--|:--|
| Unbuffered | open/read/write/close | Direct system calls, immediate kernel interaction |
| Buffered | fopen/fread/fwrite/fclose | Uses stdio library buffer for efficiency |

---

## 12️⃣ Difference between `stat()` and `lstat()`
- `stat()` → follows symbolic links.  
- `lstat()` → returns info about the link itself.

---

## 13️⃣ What is contained in `struct file`?
- Pointer to inode  
- Current file offset  
- File flags (O_APPEND, O_NONBLOCK)  
- Pointer to file operations (`file_operations`)  
- Reference count  

---

## 14️⃣ Where is the base address of the file object stored?
In the **File Descriptor Table** — each entry maps `fd → struct file *`.

---

## 15️⃣ When is the File Descriptor Table created?
Created during **process creation**, and inherited by children after `fork()`.

---

## 16️⃣ What is `dup()` / `dup2()` used for?
To **duplicate file descriptors** — used in I/O redirection (e.g., shell pipelines, IPC).

---

## 17️⃣ What happens during a `read()` system call?
1. Kernel validates fd → `struct file`.  
2. Checks permissions and offset.  
3. Invokes driver’s `read()` via `file_operations`.  
4. Copies data to user buffer.  
5. Updates offset and returns byte count.

---

## 18️⃣ What happens during a `write()` system call?
Similar process:
- Data copied from user → kernel buffer → device or file blocks.  
- Offset advanced, metadata updated.

---

## 19️⃣ What is `lseek()` used for?
Changes the **current file offset** (cursor) within an open file.

---

## 20️⃣ Can multiple processes open the same file?
Yes — each has its own `struct file`, pointing to the same **inode**.

---

## 21️⃣ Hard Links vs Symbolic Links
| Type | Description |
|:--|:--|
| **Hard link** | Another directory entry pointing to the same inode |
| **Symbolic link** | A special file containing a pathname to the target |

---

## 22️⃣ File Permissions Representation
Nine bits: `rwx rwx rwx` → User, Group, Others.  
Example: `rw-r--r--` → Owner read/write, others read only.

---

## 23️⃣ View or Change Permissions
- **View:** `ls -l`  
- **Change:** `chmod`, `chown`, `chgrp`

---

## 24️⃣ What is umask?
A **mask** of default permission bits that are **turned off** when new files are created.

---

## 25️⃣ What is `ioctl()`?
A system call that allows **user programs to send device-specific control commands** to a driver.

---

## 26️⃣ Block vs Character Devices

| Type | Access | Examples |
|:--|:--|:--|
| Character Device | Byte-stream I/O | Serial ports, terminals |
| Block Device | Block-based I/O (cached) | Disks, USB drives |

---

## 27️⃣ What is the Buffer Cache?
A kernel cache that stores **recently used disk blocks** in RAM to speed up file access and reduce I/O.

---

## 28️⃣ What is the Page Cache?
Holds **file data pages** for both reads and writes — unified caching for file and memory-mapped I/O.

---

## 29️⃣ What is Asynchronous I/O (AIO)?
Allows operations like `aio_read()` and `aio_write()` to run concurrently **without blocking** — app gets notified when complete.

---

## 30️⃣ What is Direct I/O?
Bypasses the page cache — used in **high-performance apps** (like databases) that manage their own buffers.

---

## 31️⃣ What is Memory-Mapped I/O?
Using `mmap()`, a file’s contents are mapped into **virtual memory**, so reads/writes become memory accesses.

---

## 32️⃣ What are File System Mount Points?
Directories where additional file systems are attached.  
Example:
```bash
mount /dev/sda1 /mnt/data
````

---

## 33️⃣ What is `/proc` in Linux?

A **pseudo-filesystem** exposing kernel and process information as **virtual files** — no data stored on disk.

---

## 34️⃣ What is `/sys` filesystem?

**Sysfs** exposes info about kernel objects (devices, drivers, subsystems).
Used by `udev` and system tools.

---

## 35️⃣ What is Journaling in File Systems?

A mechanism that records **metadata changes** in a **log (journal)** before applying them — ensures recovery after crashes.
Used by **ext4, xfs, btrfs**.

---

## 36️⃣ Common Linux File Systems

`ext4`, `xfs`, `btrfs`, `f2fs`, `vfat`, `ntfs`, `tmpfs`, `procfs`.

---

## 37️⃣ What is tmpfs?

A **temporary, memory-backed filesystem** — fast, volatile, and cleared on reboot.
Commonly mounted on `/tmp` or `/run`.

---

## 38️⃣ Absolute vs Relative Paths

| Type     | Example               |
| :------- | :-------------------- |
| Absolute | `/home/hari/file.txt` |
| Relative | `../docs/file.txt`    |

---

## 39️⃣ What is the Superblock?

Contains global file-system metadata such as:

* Total size
* Block count
* Inode count
* Mount info
* File-system state

---

## 40️⃣ File System Mount Table

A kernel table listing all mounted filesystems.
Viewable with:

```bash
cat /proc/mounts
# or
mount
```

---

## 41️⃣ How does Linux handle device files?

Each device appears under `/dev` with **major and minor numbers** identifying its driver and device instance.

---

## 42️⃣ What is udev?

A **user-space daemon** that dynamically manages `/dev` entries when devices are added or removed.

---

## 43️⃣ File System Permissions (Kernel Level)

Enforced via **inodes → UID, GID, and permission bits**.
Checked before each access.

---

## 44️⃣ Special Permission Bits

| Bit            | Description                                 |
| :------------- | :------------------------------------------ |
| setuid (s)     | Run program as file owner                   |
| setgid (s)     | Run program as group owner                  |
| sticky bit (t) | On directories → only file owner can delete |

---

## 45️⃣ What is `fsync()`?

Flushes all **buffered data** of a file to disk — ensures data integrity.

---

## 46️⃣ What is `select()` / `poll()` / `epoll()`?

System calls for **I/O multiplexing** — monitor multiple file descriptors and wait until any is ready (used in servers).

---

## 47️⃣ Blocking vs Non-blocking I/O

| Type         | Description                          |
| :----------- | :----------------------------------- |
| Blocking     | Call waits until operation completes |
| Non-blocking | Returns immediately; can poll later  |

---

## 48️⃣ What is `readdir()` / `getdents()`?

* `readdir()` (user) reads directory entries.
* Internally, kernel uses `getdents()` syscall.

---

## 49️⃣ How does Linux unify files and devices?

**Everything is a file.**
Devices, sockets, pipes — all exposed as file descriptors and operated via standard I/O calls.

---

## 50️⃣ What are File System Quotas?

**Limits** on users/groups for disk usage (blocks/inodes).
Managed by:
`edquota`, `repquota`, `quotaon`.

---

✅ **Summary**

* VFS provides a uniform interface for multiple file systems.
* inodes store metadata; dentries map filenames → inodes.
* File descriptors index into process-specific file tables.
* Buffers, caches, and journaling ensure performance and reliability.
* Everything in Linux — files, devices, sockets — is managed as a file.

```
