# Qualcomm Interview Questions on File Locks (with Answers)

## File Locking System Calls: `fcntl()`, `lockf()`, `flock()`

### 1. What is the purpose of file locking in Linux?
**Answer:** To prevent concurrent access to files, ensuring data integrity during read/write operations.

### 2. What are the main types of file locks?
**Answer:** Advisory and mandatory locks; advisory is most common in Linux.

### 3. What does `fcntl()` do in the context of file locking?
**Answer:** It manages file descriptors and can set, get, or remove advisory record locks.

### 4. What are the lock types used with `fcntl()`?
**Answer:** `F_RDLCK`, `F_WRLCK`, `F_UNLCK`.

### 5. What is `F_SETLK` in `fcntl()`?
**Answer:** Sets a non-blocking lock on a file.

### 6. What is `F_SETLKW` in `fcntl()`?
**Answer:** Sets a blocking lock and waits until the lock is available.

### 7. What structure is used with `fcntl()` for locking?
**Answer:** `struct flock`, which specifies the lock type, offset, and length.

### 8. How do you unlock a file using `fcntl()`?
**Answer:** Use `F_UNLCK` with `F_SETLK`.

### 9. What does `lockf()` do?
**Answer:** Applies or removes advisory locks on open files, built on top of `fcntl()`.

### 10. What is the difference between `lockf()` and `fcntl()`?
**Answer:** `lockf()` is simpler but less flexible; internally uses `fcntl()`.

### 11. What are the common `lockf()` commands?
**Answer:** `F_LOCK`, `F_TLOCK`, `F_ULOCK`, `F_TEST`.

### 12. What does `F_TLOCK` do in `lockf()`?
**Answer:** Attempts to place a non-blocking lock.

### 13. What does `F_LOCK` do in `lockf()`?
**Answer:** Places a blocking lock on the file.

### 14. How can you test if a lock is available using `lockf()`?
**Answer:** Use `F_TEST`.

### 15. What does `flock()` do?
**Answer:** Applies or removes an advisory lock on an entire file.

### 16. What are the lock types used with `flock()`?
**Answer:** `LOCK_SH` (shared), `LOCK_EX` (exclusive), `LOCK_UN` (unlock).

### 17. What does `LOCK_NB` do?
**Answer:** Used with `LOCK_SH` or `LOCK_EX` to make the lock request non-blocking.

### 18. Can `flock()` and `fcntl()` be used together?
**Answer:** No, they are independent and not compatible.

### 19. What is the scope of a `flock()` lock?
**Answer:** It applies to the entire file.

### 20. What is the scope of a `fcntl()` lock?
**Answer:** It can lock specific byte ranges (record locking).

---

## Advanced Behavior and Edge Cases

### 21. Are file locks inherited across `fork()`?
**Answer:** Yes, the child inherits the locks.

### 22. Are locks preserved across `exec()`?
**Answer:** Yes, if the file descriptor remains open.

### 23. Are locks automatically released on file close?
**Answer:** Yes, when the last reference to a locked file is closed.

### 24. Can two processes share a `LOCK_SH` lock?
**Answer:** Yes, shared locks allow concurrent reads.

### 25. Can a process upgrade from `LOCK_SH` to `LOCK_EX`?
**Answer:** Yes, but the lock must be released and reacquired.

### 26. What happens if a process holding an exclusive lock crashes?
**Answer:** The kernel releases the lock automatically.

### 27. Can you detect if a file is locked?
**Answer:** Attempt to acquire the lock non-blocking and check the result.

### 28. What happens when `fcntl()` fails to acquire a lock?
**Answer:** It returns `-1` and sets `errno` to `EACCES` or `EAGAIN`.

### 29. What happens if you lock a file that’s already locked by the same process?
**Answer:** The lock is updated; duplicate locks are not created.

### 30. Are locks preserved after file descriptor duplication (`dup()`)?
**Answer:** Yes, because they refer to the same underlying open file.

### 31. Can two threads in the same process interfere with each other's locks?
**Answer:** No, locks are maintained per-process.

### 32. Can you apply multiple `fcntl()` locks on the same file?
**Answer:** Yes, on different regions.

### 33. Can locks span multiple file descriptors?
**Answer:** Only if they refer to the same file.

### 34. Are locks advisory or mandatory by default?
**Answer:** Advisory.

### 35. How to enable mandatory locking?
**Answer:** Set group ID bit and remove group execute permission on the file.

### 36. Are `flock()` locks visible in `/proc/locks`?
**Answer:** No, only `fcntl()` locks are listed.

### 37. Which lock type is more portable: `flock()` or `fcntl()`?
**Answer:** `fcntl()`.

### 38. Which lock type is easier to use?
**Answer:** `flock()`.

### 39. Can `fcntl()` lock cover zero length?
**Answer:** Yes, interpreted as locking to EOF.

### 40. Can `fcntl()` and `lockf()` operate on the same file?
**Answer:** Yes, since `lockf()` uses `fcntl()`.

---

## Practical Scenarios

### 41. How to prevent race conditions with file locking?
**Answer:** Use exclusive locks (`LOCK_EX`, `F_WRLCK`) before reading/writing.

### 42. How to check if another process is holding a lock?
**Answer:** Use `fcntl()` with `F_GETLK`.

### 43. What is deadlock in file locking?
**Answer:** When two processes wait for each other’s locks indefinitely.

### 44. How can deadlock be avoided?
**Answer:** Use timeout mechanisms, order locking consistently, avoid nested locks.

### 45. Can a process lock a file multiple times?
**Answer:** Yes, as long as they are for different byte ranges.

### 46. How can you log locking activity?
**Answer:** Wrap locking functions and add logging or use `strace`.

### 47. What error does `flock()` return if lock cannot be acquired?
**Answer:** `-1` with `EWOULDBLOCK`.

### 48. Can locks be inherited by file descriptors passed over Unix domain sockets?
**Answer:** No, locks are per process, not file descriptor.

### 49. Is there a way to visualize lock ownership?
**Answer:** Inspect `/proc/locks` or use tools like `lsof`.

### 50. Can you lock a file opened in read-only mode?
**Answer:** Yes, but only shared (read) locks.

### 51. Can a read-only file descriptor hold a write lock?
**Answer:** No, a write lock requires write permission on the descriptor.

### 52. Are file locks effective across NFS?
**Answer:** Not reliably; NFS support for file locking varies by implementation.

---

*Prepared for Qualcomm/Linux system programming interviews — Topic: File Locks*

