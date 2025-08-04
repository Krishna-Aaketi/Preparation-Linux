## File I/O and Descriptors – Questions with Answers

1. **What is a file descriptor?**
   **Answer:** A file descriptor is a non-negative integer returned by system calls like `open()`, `pipe()`, or `socket()` to reference an open file, pipe, or socket.

2. **How does the kernel manage file descriptors internally?**
   **Answer:** Each process has a file descriptor table pointing to entries in the system-wide open file table, which in turn points to inodes.

3. **What is the maximum number of file descriptors a process can open?**
   **Answer:** It depends on system limits. Use `ulimit -n` or `getrlimit()` to check. Typically 1024 by default on many systems.

4. **What is the difference between file descriptor and file pointer?**
   **Answer:** A file descriptor is used in low-level I/O (e.g., `read()`), whereas a file pointer (FILE*) is used in high-level I/O (`fread()`, `fopen()`).

5. **Explain the use of open(), read(), write(), and close().**
   **Answer:** `open()` opens a file and returns a file descriptor. `read()` and `write()` perform data transfer. `close()` releases the descriptor.

6. **What is the significance of O_CREAT and O_TRUNC flags?**
   **Answer:** `O_CREAT` creates the file if it doesn’t exist. `O_TRUNC` truncates the file to 0 length if it exists.

7. **How does lseek() work and what are its typical use cases?**
   **Answer:** `lseek()` repositions the read/write offset of a file descriptor. Useful for random access in files.

8. **What is the difference between lseek() and pread()/pwrite()?**
   **Answer:** `pread()` and `pwrite()` do not change the file offset, allowing thread-safe I/O.

9. **What are the possible return values of read() and write(), and what do they mean?**
   **Answer:** Positive: number of bytes processed, 0: EOF (read only), -1: error.

10. **What happens if you read() from a file opened with O_WRONLY?**
   **Answer:** It fails with `EBADF` (bad file descriptor) error.

11. **What is the use of O_APPEND and how does it affect write()?**
   **Answer:** Ensures all writes append data to the end of the file.

12. **What does O_NONBLOCK do?**
   **Answer:** Makes I/O operations non-blocking, returning immediately if data isn’t available.

13. **How do you check for file existence before opening it?**
   **Answer:** Use `access(filename, F_OK)`.

14. **Explain the concept of blocking vs non-blocking I/O.**
   **Answer:** Blocking waits for the operation to complete. Non-blocking returns immediately if it can’t proceed.

15. **What is a file mode and how does it affect open()?**
   **Answer:** Defines access type: `O_RDONLY`, `O_WRONLY`, `O_RDWR`. It controls allowed operations.

16. **Explain how dup() and dup2() work.**
   **Answer:** They duplicate file descriptors. `dup2()` allows specifying the target descriptor.

17. **What is the difference between dup2() and dup3()?**
   **Answer:** `dup3()` is like `dup2()` but allows `O_CLOEXEC` and is atomic.

18. **How do you redirect stdout to a file using system calls?**
   **Answer:** Use `dup2(fd, STDOUT_FILENO)` after opening the file.

19. **What happens if you close an already closed file descriptor?**
   **Answer:** Undefined behavior, often results in `EBADF`.

20. **How do pipes use file descriptors?**
   **Answer:** `pipe()` returns two FDs: one for reading, one for writing.

21. **How does the kernel associate open files with processes?**
   **Answer:** Through the process’s file descriptor table.

22. **What is the system-wide file table?**
   **Answer:** A global table containing metadata for all open files.

23. **Can two processes share the same file descriptor? How?**
   **Answer:** Yes, if inherited from `fork()` or passed via UNIX domain socket.

24. **What is an inode and how is it linked to file descriptors?**
   **Answer:** An inode holds metadata. File descriptors indirectly point to it via open file entries.

25. **What is the behavior of read() when reaching EOF?**
   **Answer:** Returns 0.

26. **Explain how buffered and unbuffered I/O differ.**
   **Answer:** Buffered I/O (e.g., stdio) uses memory buffers. Unbuffered I/O (e.g., read()) does not.

27. **What is the significance of the return value of lseek()?**
   **Answer:** It returns the new offset or -1 on error.

28. **Can lseek() be used on sockets or pipes?**
   **Answer:** No, it returns `ESPIPE`.

29. **What is the purpose of the access() system call?**
   **Answer:** Checks file permissions without opening it.

30. **How do you use fcntl() to manipulate file descriptors?**
   **Answer:** Modify FD flags (`F_GETFL`, `F_SETFL`), duplicate FDs, manage file locks.

31. **How to make a file descriptor non-blocking using fcntl()?**
   **Answer:** `fcntl(fd, F_SETFL, O_NONBLOCK)`.

32. **What is the use of O_DIRECT?**
   **Answer:** Performs I/O without buffering in kernel, for high-performance needs.

33. **How does the kernel ensure consistency during concurrent file access?**
   **Answer:** Through locks (flock, fcntl), file system semantics, and atomic ops.

34. **How is O_EXCL used in open()?**
   **Answer:** Ensures creation fails if the file exists (used with O_CREAT).

35. **What are soft and hard file descriptor limits?**
   **Answer:** Soft: current limit, can be changed by process. Hard: max allowed.

36. **What is the difference between STDIN_FILENO, STDOUT_FILENO, STDERR_FILENO?**
   **Answer:** Standard file descriptor constants: 0, 1, and 2 respectively.

37. **How to safely read a binary file using system calls?**
   **Answer:** Use `open()` with `O_RDONLY` and `read()` into a properly sized buffer.

38. **What is the difference between fread() and read()?**
   **Answer:** `fread()` is buffered (stdio), `read()` is unbuffered (syscall).

39. **How to implement atomic file writes using system calls?**
   **Answer:** Use `O_APPEND`, or write small records that are atomic by size.

40. **What are sparse files and how do they affect file I/O?**
   **Answer:** Files with unallocated blocks. Reads from holes return zero.

41. **How does the kernel handle file deletion when it is still open?**
   **Answer:** File is removed from dir, but data remains until all FDs are closed.

42. **Explain the role of umask in file creation.**
   **Answer:** Masks permission bits during file creation.

43. **How to open a temporary file securely using mkstemp()?**
   **Answer:** `mkstemp()` creates a unique file and returns a safe FD.

44. **What is memory-mapped I/O and how is it different from read/write?**
   **Answer:** Maps file into memory for direct access. Avoids syscall overhead.

45. **How does the kernel handle concurrent read/write?**
   **Answer:** Uses locks, VFS layer coordination, and atomic ops.

46. **What is the purpose of the sync() system call?**
   **Answer:** Flushes all kernel buffers to disk.

47. **How to flush file buffers manually?**
   **Answer:** Use `fsync(fd)` or `fdatasync(fd)`.

48. **What is O_CLOEXEC and when is it used?**
   **Answer:** Auto-closes FD on `exec()` call. Use in multithreaded apps.

49. **How to monitor file descriptor leaks in a process?**
   **Answer:** Use `lsof`, `/proc/<pid>/fd`, or Valgrind.

50. **Can file descriptors be shared between parent and child processes?**
   **Answer:** Yes, descriptors are inherited during `fork()`.

51. **What happens if you close STDOUT_FILENO and then open a new file?**
   **Answer:** New file takes descriptor 1 (STDOUT), redirecting stdout to it.

## File and Directory Management – 51 Interview Questions with Answers

### 1. What is the difference between a file and a directory?
**Answer:** A file stores data (text, binary, etc.), while a directory is a container that holds files and other directories. Directories organize the file system hierarchy.

### 2. How do you create and delete a file in C using system calls?
**Answer:** Use `open()` with `O_CREAT` flag to create, and `unlink()` to delete.
```c
int fd = open("file.txt", O_CREAT | O_WRONLY, 0644);
unlink("file.txt");
```

### 3. How do you create and delete a directory?
**Answer:** Use `mkdir("dir_name", mode)` to create and `rmdir("dir_name")` to delete.

### 4. Explain the use of stat(), lstat(), and fstat().
**Answer:**
- `stat()` gives info about the file pointed to by the path.
- `lstat()` is similar but doesn’t follow symlinks.
- `fstat()` works on an open file descriptor.

### 5. What is an inode and what information does it contain?
**Answer:** Inode is a data structure storing metadata like file size, ownership, permissions, timestamps, etc., but not filename.

### 6. What is the difference between a hard link and a soft link?
**Answer:**
- Hard link: Direct reference to the same inode. Deleting the original doesn’t affect it.
- Soft link: A file pointing to another file path. Broken if the target is deleted.

### 7. How do you create a symbolic link in C?
**Answer:** Use the `symlink("target", "link")` system call.

### 8. What is the significance of file permissions?
**Answer:** They control access: read (r), write (w), execute (x) for user, group, and others.

### 9. How does chmod() work at the system call level?
**Answer:** `chmod("file", mode)` changes file permissions using the provided octal mode (e.g., 0755).

### 10. What is the difference between access(), stat(), and open()?
**Answer:**
- `access()`: Checks real user permissions.
- `stat()`: Fetches file metadata.
- `open()`: Opens file based on effective user ID.

### 11. What is the role of umask?
**Answer:** Umask masks default permission bits during file/directory creation. E.g., 022 results in default 755 dir perms.

### 12. How to change ownership of a file?
**Answer:** Use `chown("file", uid, gid)` or `fchown(fd, uid, gid)`.

### 13. What is the difference between unlink() and remove()?
**Answer:** Both delete a file, but `remove()` is standard library; `unlink()` is POSIX system call.

### 14. How to get the current working directory in a program?
**Answer:** Use `getcwd(buffer, size)`.

### 15. What does chdir() do?
**Answer:** It changes the current working directory of the process.

### 16. How do you list files in a directory using system calls?
**Answer:** Use `opendir()`, `readdir()`, `closedir()` to iterate entries.

### 17. How is opendir(), readdir(), closedir() implemented internally?
**Answer:** These are wrappers over `open()`, `getdents()`, and `close()` system calls.

### 18. How to distinguish between a file, directory, and symbolic link?
**Answer:** Use `stat()` or `lstat()` and check `st_mode` using `S_ISREG`, `S_ISDIR`, `S_ISLNK` macros.

### 19. What is the use of realpath()?
**Answer:** It resolves absolute, canonical path including symlinks and `..`, `.`.

### 20. How to traverse directories recursively in C?
**Answer:** Use `opendir()` + recursion, and avoid infinite loops by checking `.` and `..`.

### 21. What is the "." and ".." directory entries?
**Answer:** `.` refers to current directory, `..` refers to parent directory.

### 22. Can you delete a directory that is not empty?
**Answer:** Not with `rmdir()`. You must recursively delete all contents first.

### 23. What happens to a hard link when the original file is deleted?
**Answer:** Nothing. The hard link remains because it references the same inode.

### 24. Can symbolic links be circular?
**Answer:** Yes, and it causes infinite loops unless detected (e.g., by `realpath()`).

### 25. How to handle file names with special characters?
**Answer:** Use quotes or escape characters properly. Handle filenames as binary-safe strings in code.

### 26. What is the maximum filename length on Linux?
**Answer:** 255 bytes (not characters) in most filesystems like ext4.

### 27. How does Linux handle long path names?
**Answer:** Maximum path length is 4096 bytes (PATH_MAX). Use relative paths or split processing.

### 28. What is file truncation?
**Answer:** It shortens a file to zero length using `truncate()` or `O_TRUNC` flag.

### 29. How do you check if a file is a regular file or device?
**Answer:** Use `stat()` and `S_ISREG()` / `S_ISCHR()` / `S_ISBLK()` macros.

### 30. What is the difference between stat.st_size and actual disk usage?
**Answer:** `st_size` is logical file size; disk usage includes blocks allocated, sparse holes excluded.

### 31. What is a file hole in sparse files?
**Answer:** A region with no disk blocks allocated, logically zero, created with `lseek()`.

### 32. How do you open a file relative to a directory file descriptor?
**Answer:** Use `openat(dirfd, "file", flags)`.

### 33. What are file attributes and how do you view/modify them?
**Answer:** Attributes include immutable, append-only, etc. Use `lsattr`, `chattr` tools.

### 34. How do extended attributes work in Linux?
**Answer:** They store metadata beyond standard attributes. Use `getfattr`, `setfattr`.

### 35. What is sticky bit, and when is it used?
**Answer:** In shared directories like `/tmp`, it prevents users from deleting others’ files.

### 36. What are SUID and SGID?
**Answer:**
- SUID: Executed with file owner’s permissions.
- SGID: Executed with group permissions or shared group ownership in directories.

### 37. What happens if you create a file with no execute permission?
**Answer:** It can’t be executed, only read/written. Attempting to execute will fail.

### 38. What is the use of fsync() and fdatasync()?
**Answer:** These flush buffers to disk:
- `fsync()` flushes all (metadata + data).
- `fdatasync()` flushes only data.

### 39. What is the purpose of mkfifo()?
**Answer:** It creates a named pipe (FIFO), allowing unrelated processes to communicate.

### 40. What is a file descriptor leak in directory operations?
**Answer:** Forgetting to call `closedir()` causes resource leaks.

### 41. How are files indexed in ext4 filesystem?
**Answer:** Via inodes and directory entry hashes (HTree index for faster lookup).

### 42. How does the kernel maintain consistency in directory operations?
**Answer:** Through journaling (if supported), locking, and atomicity of syscalls like `rename()`.

### 43. What is the role of dentry cache?
**Answer:** Dentry cache speeds up path resolution by storing recently used directory entries.

### 44. How is file deletion handled when multiple processes have it open?
**Answer:** The file remains until all file descriptors are closed, then the inode is deleted.

### 45. What are journaling filesystems?
**Answer:** They log metadata operations before applying them to disk, ensuring recovery after crashes.

### 46. What are the steps involved in renaming a file atomically?
**Answer:** Use `rename("old", "new")`; it is atomic and overwrites target if it exists.

### 47. What is the difference between tmpfile() and mkstemp()?
**Answer:**
- `tmpfile()` creates unnamed temporary files.
- `mkstemp()` creates secure, named temporary files.

### 48. What are ACLs and how do they enhance standard file permissions?
**Answer:** ACLs allow per-user/per-group permissions beyond traditional 9-bit mode.

### 49. How do you check and modify file timestamps?
**Answer:** Use `stat()` to read, `utimensat()` or `touch` to modify.

### 50. What is the purpose of link() system call?
**Answer:** It creates a hard link pointing to the same inode as an existing file.

### 51. How to monitor file and directory events using inotify?
**Answer:** Use `inotify_init()`, `inotify_add_watch()`, and read events from the descriptor.
