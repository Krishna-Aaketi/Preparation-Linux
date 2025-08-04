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

