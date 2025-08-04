# Qualcomm Interview Questions on Polling and Event Notification (with Answers)

## I/O Multiplexing APIs: `select()`, `poll()`, `epoll()`

### 1. What is the purpose of `select()`?
**Answer:** `select()` monitors multiple file descriptors to see if any are ready for I/O.

### 2. What are the limitations of `select()`?
**Answer:** Limited to `FD_SETSIZE` (typically 1024), linear scan overhead.

### 3. How does `poll()` differ from `select()`?
**Answer:** Uses an array of `pollfd` structures, no hard FD limit, and more efficient iteration.

### 4. What are the common events used in `poll()`?
**Answer:** `POLLIN`, `POLLOUT`, `POLLERR`, `POLLHUP`

### 5. What is `epoll()` and why is it preferred?
**Answer:** A scalable I/O event notification system; better performance for large numbers of FDs.

### 6. How does `epoll` improve scalability?
**Answer:** Uses O(1) time complexity due to internal event lists and readiness notification.

### 7. What are the three primary `epoll` system calls?
**Answer:** `epoll_create1()`, `epoll_ctl()`, `epoll_wait()`

### 8. What is the use of `epoll_create1()`?
**Answer:** Creates an epoll instance and returns a file descriptor.

### 9. How are FDs added to `epoll`?
**Answer:** Using `epoll_ctl()` with `EPOLL_CTL_ADD`, `EPOLL_CTL_MOD`, `EPOLL_CTL_DEL`

### 10. What are `EPOLLIN` and `EPOLLOUT`?
**Answer:** `EPOLLIN` signals readiness to read, `EPOLLOUT` signals readiness to write.

### 11. What is edge-triggered vs level-triggered in `epoll`?
**Answer:** Edge-triggered reports only state changes; level-triggered reports continuously until state clears.

### 12. How to make `epoll` edge-triggered?
**Answer:** Use `EPOLLET` flag in `epoll_ctl()`.

### 13. What happens if `epoll_wait()` times out?
**Answer:** Returns 0.

### 14. What is the return value of `epoll_wait()` on success?
**Answer:** Number of triggered events.

### 15. How can you monitor stdin and socket together?
**Answer:** Add both FDs to `select()`/`poll()`/`epoll` interest list.

---

## File Descriptors for Events: `signalfd()`, `eventfd()`, `timerfd()`

### 16. What does `signalfd()` do?
**Answer:** Receives signals via a file descriptor instead of async delivery.

### 17. Why use `signalfd()`?
**Answer:** Enables signal handling within `select()`/`epoll()` loops.

### 18. What structure does `signalfd()` return data in?
**Answer:** `struct signalfd_siginfo`

### 19. What signals can be received through `signalfd()`?
**Answer:** Any signals blocked by the process using `sigprocmask()`.

### 20. What is `eventfd()` used for?
**Answer:** Provides a simple counter for inter-thread or inter-process signaling.

### 21. What values can `eventfd()` return?
**Answer:** The current value of the internal counter.

### 22. What are common use cases for `eventfd()`?
**Answer:** Thread wake-up notifications, semaphores, producer-consumer queues.

### 23. What does `EFD_SEMAPHORE` flag do?
**Answer:** Makes `read()` on `eventfd` return 1 instead of the full counter value.

### 24. What is `timerfd_create()`?
**Answer:** Creates a timer that notifies via a file descriptor.

### 25. What are advantages of `timerfd()` over signal-based timers?
**Answer:** Integrates cleanly with `epoll`, `select`, `poll` — avoids async signal issues.

### 26. What are `struct itimerspec` and `struct timespec` used for?
**Answer:** Define timer intervals and expiration times.

### 27. What does reading from `timerfd` return?
**Answer:** The number of expirations that occurred.

### 28. How to make a timerfd periodic?
**Answer:** Set `it_interval` in `itimerspec` to non-zero.

### 29. Can `signalfd()` be used with `fork()`?
**Answer:** Yes, but FD and signal masks should be reconfigured in child.

### 30. Can `epoll` monitor `eventfd`, `timerfd`, and `signalfd`?
**Answer:** Yes — this is one of its major advantages.

---

## Practical Questions

### 31. What are the common problems with `select()`?
**Answer:** FD size limit, inefficient scanning, poor scalability.

### 32. Why might `poll()` be preferred over `select()`?
**Answer:** No FD_SET size limit and easier structure.

### 33. How do you avoid race conditions with `epoll()`?
**Answer:** Use non-blocking I/O and handle `EPOLLHUP`, `EPOLLERR`.

### 34. What happens when `epoll` FD is closed?
**Answer:** All watched FDs are also released.

### 35. Can you use edge-triggered mode safely?
**Answer:** Yes, but requires non-blocking I/O and full buffer drain.

### 36. How to unregister FD from `epoll`?
**Answer:** Use `epoll_ctl()` with `EPOLL_CTL_DEL`.

### 37. Is `epoll` thread-safe?
**Answer:** Yes, but access must be synchronized when multiple threads use it.

### 38. What is the difference between blocking and non-blocking FDs in `poll()`?
**Answer:** Blocking FDs can cause stalls; non-blocking allows event-driven design.

### 39. How can `select()` timeout be specified?
**Answer:** Use `struct timeval` with seconds and microseconds.

### 40. Can you detect EOF with `select()` or `poll()`?
**Answer:** Yes, use `POLLHUP` or `read()` returning 0.

### 41. Why use `timerfd` in real-time systems?
**Answer:** Predictable, fine-grained timers that avoid signal delivery latency.

### 42. Can `eventfd()` handle multiple producers and consumers?
**Answer:** Yes, but must be used with care to avoid lost wakeups.

### 43. What file descriptor types cannot be monitored by `epoll`?
**Answer:** Some special files (e.g., regular files on disk) do not generate events.

### 44. What is `EPOLLERR`?
**Answer:** Indicates an error occurred on the FD.

### 45. What is `EPOLLHUP`?
**Answer:** Peer closed connection — hang up.

### 46. How does `poll()` handle large number of FDs?
**Answer:** Linear scan; less efficient than `epoll` for large FD sets.

### 47. What happens when writing to a full `eventfd()`?
**Answer:** Write blocks or fails with `EAGAIN` if non-blocking.

### 48. Can `eventfd` replace condition variables?
**Answer:** In some cases, yes — it is a low-level signal mechanism.

### 49. Why prefer `signalfd()` in event-driven apps?
**Answer:** Integrates signal handling into main event loop.

### 50. Can `select()`, `poll()`, and `epoll()` be mixed?
**Answer:** Technically yes, but not recommended — use one model consistently.

---

*Prepared for Qualcomm/Linux system programming interviews — Topic: Polling and Event Notification*

