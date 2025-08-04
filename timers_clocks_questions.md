# Qualcomm Interview Questions on Timers and Clocks (with Answers)

## Time Retrieval Functions: `time()`, `gettimeofday()`, `clock_gettime()`

### 1. What does `time()` return?
**Answer:** It returns the current time in seconds since the Epoch (January 1, 1970).

### 2. What is the return type of `time()`?
**Answer:** `time_t`

### 3. What is `gettimeofday()` used for?
**Answer:** It provides the current time with microsecond precision.

### 4. What structure does `gettimeofday()` fill?
**Answer:** `struct timeval`, containing `tv_sec` and `tv_usec`.

### 5. Is `gettimeofday()` POSIX compliant?
**Answer:** It is obsolete in POSIX.1-2008; `clock_gettime()` is recommended.

### 6. What is `clock_gettime()`?
**Answer:** It gets the current time of the specified clock (e.g., real-time, monotonic).

### 7. What structure is used with `clock_gettime()`?
**Answer:** `struct timespec`, which has `tv_sec` and `tv_nsec`.

### 8. What are the common clock types for `clock_gettime()`?
**Answer:** `CLOCK_REALTIME`, `CLOCK_MONOTONIC`, `CLOCK_PROCESS_CPUTIME_ID`, `CLOCK_THREAD_CPUTIME_ID`

### 9. What is `CLOCK_REALTIME`?
**Answer:** System-wide real-time clock, affected by NTP or manual changes.

### 10. What is `CLOCK_MONOTONIC`?
**Answer:** Clock that cannot be set and represents monotonic time since boot.

---

## POSIX Timers: `timer_create()`, `timer_settime()`

### 11. What is `timer_create()` used for?
**Answer:** It creates a POSIX per-process timer.

### 12. What is the return value of `timer_create()`?
**Answer:** `0` on success, `-1` on error.

### 13. What arguments does `timer_create()` take?
**Answer:** A clock ID, a `sigevent` structure, and a pointer to `timer_t`.

### 14. What is the role of `struct sigevent` in `timer_create()`?
**Answer:** It defines how the process is notified when the timer expires.

### 15. What are common `sigevent` notification types?
**Answer:** `SIGEV_SIGNAL`, `SIGEV_THREAD`, `SIGEV_NONE`

### 16. What is `timer_settime()` used for?
**Answer:** Starts or arms a timer created with `timer_create()`.

### 17. What structure does `timer_settime()` use to configure the timer?
**Answer:** `struct itimerspec`, which contains `it_value` and `it_interval`.

### 18. What is the purpose of `it_value` in `itimerspec`?
**Answer:** Specifies the time until the next expiration.

### 19. What is the purpose of `it_interval`?
**Answer:** Defines the period for recurring expirations (0 means one-shot).

### 20. How can you disarm a timer?
**Answer:** Set `it_value.tv_sec` and `tv_nsec` to 0.

---

## Advanced Usage and Edge Cases

### 21. How is a timer deleted?
**Answer:** Using `timer_delete(timer_t timerid)`.

### 22. Can multiple timers be created in a process?
**Answer:** Yes, each with its own `timer_t` ID.

### 23. What happens if a timer expires and no signal handler is installed?
**Answer:** The signal is discarded if unhandled.

### 24. What unit of time does `clock_gettime()` provide?
**Answer:** Seconds and nanoseconds.

### 25. Is `clock_gettime()` affected by system clock changes?
**Answer:** Depends on the clock; `CLOCK_REALTIME` is, but `CLOCK_MONOTONIC` is not.

### 26. How do you measure elapsed time between two events?
**Answer:** Capture two `timespec` values and subtract them.

### 27. What are the advantages of `clock_gettime()` over `gettimeofday()`?
**Answer:** Higher precision, multiple clock sources, and POSIX compliance.

### 28. What is the maximum resolution provided by `clock_gettime()`?
**Answer:** Nanosecond resolution.

### 29. How does `clock_nanosleep()` work?
**Answer:** Suspends execution with nanosecond precision.

### 30. How is a periodic timer configured?
**Answer:** Set both `it_value` and `it_interval` in `itimerspec`.

### 31. Can `SIGEV_THREAD` be used to trigger a function directly?
**Answer:** Yes, it starts a new thread to run the function on timer expiration.

### 32. What are real use cases of POSIX timers?
**Answer:** Real-time systems, task scheduling, performance monitoring.

### 33. Can POSIX timers be used in multithreaded programs?
**Answer:** Yes, especially with `SIGEV_THREAD` to notify specific threads.

### 34. How is `timer_gettime()` used?
**Answer:** Retrieves the remaining time and interval of a running timer.

### 35. How is `timer_getoverrun()` useful?
**Answer:** Indicates missed expirations for signal-based timers.

---

## Practical Scenarios

### 36. How do you safely measure execution time of a function?
**Answer:** Use `clock_gettime()` before and after the function call.

### 37. How to implement a one-shot timer?
**Answer:** Set `it_value`, and `it_interval` to 0.

### 38. How to implement a periodic heartbeat every 1 second?
**Answer:** Set both `it_value` and `it_interval` to 1 second.

### 39. What is the behavior of timer if `it_value` is zero?
**Answer:** The timer is disarmed.

### 40. Can timers send custom signals?
**Answer:** Yes, `sigev_signo` can be customized.

### 41. Are timers shared across processes?
**Answer:** No, they are per-process.

### 42. Is it possible to have high-resolution timers?
**Answer:** Yes, with `CLOCK_MONOTONIC` or `CLOCK_REALTIME`, nanosecond precision is supported.

### 43. How to log timestamps with high precision?
**Answer:** Use `clock_gettime()` and format `tv_nsec` appropriately.

### 44. Can timers be used in signal-safe manner?
**Answer:** Use `sigaction` with `SA_RESTART` and `sig_atomic_t` variables.

### 45. How can you test timer accuracy?
**Answer:** Compare system timer output with known intervals, or use profiling tools.

### 46. Can timers cause race conditions?
**Answer:** Yes, if shared state is accessed without synchronization in signal handlers.

### 47. Which system call replaces `alarm()` for more control?
**Answer:** POSIX timers like `timer_create()` and `timer_settime()`.

### 48. How to reduce jitter in periodic timers?
**Answer:** Use `CLOCK_MONOTONIC` and avoid overloaded systems.

### 49. What is the limitation of using signals for timers?
**Answer:** Signal delivery can be delayed and is hard to manage in complex systems.

### 50. How to achieve sub-millisecond delays?
**Answer:** Use `clock_nanosleep()` with high-resolution clocks.

### 51. Can timers be paused and resumed?
**Answer:** Not directly; simulate by reading current value and resetting later.

### 52. What library functions rely on these timer syscalls internally?
**Answer:** `sleep()`, `usleep()`, `nanosleep()` and event loop timers in frameworks.

---

*Prepared for Qualcomm/Linux system programming interviews — Topic: Timers and Clocks*

