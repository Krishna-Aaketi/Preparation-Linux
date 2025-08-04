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

