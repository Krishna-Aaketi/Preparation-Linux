# Qualcomm Interview Questions on Scheduling and Priorities (with Answers)

## System Calls: `nice()`, `setpriority()`

### 1. What does the `nice()` system call do?
**Answer:** It changes the nice value (priority) of a process, affecting its scheduling priority.

### 2. What is the default nice value of a process?
**Answer:** The default nice value is 0.

### 3. What is the range of nice values in Linux?
**Answer:** From -20 (highest priority) to +19 (lowest priority).

### 4. What does a lower nice value imply?
**Answer:** Higher scheduling priority.

### 5. Who can decrease the nice value of a process (raise priority)?
**Answer:** Only the superuser (root).

### 6. What does the `setpriority()` system call do?
**Answer:** It sets the priority of a process, process group, or user.

### 7. What are the arguments to `setpriority()`?
**Answer:** `int which`, `int who`, `int prio` — specifies the target and the priority to set.

### 8. What is the corresponding function to retrieve priority?
**Answer:** `getpriority()`.

### 9. How can a user reduce a process's priority?
**Answer:** By using `nice()` with a positive value or `setpriority()`.

### 10. What is the effect of increasing the nice value?
**Answer:** The process will get less CPU time.

---

## Scheduling Policies: SCHED_FIFO, SCHED_RR, SCHED_OTHER

### 11. What is `SCHED_OTHER`?
**Answer:** The default Linux time-sharing scheduling policy for normal processes.

### 12. What is `SCHED_FIFO`?
**Answer:** A real-time, first-in-first-out scheduling policy with static priority.

### 13. What is `SCHED_RR`?
**Answer:** A real-time round-robin scheduling policy with static priority.

### 14. Which scheduling policies are real-time?
**Answer:** `SCHED_FIFO` and `SCHED_RR`.

### 15. Can non-root users use real-time scheduling policies?
**Answer:** No, real-time scheduling typically requires root privileges.

### 16. What system call is used to set scheduling policy?
**Answer:** `sched_setscheduler()`.

### 17. What system call is used to get scheduling policy?
**Answer:** `sched_getscheduler()`.

### 18. What does `sched_get_priority_min()` return?
**Answer:** The minimum static priority for a given policy.

### 19. What does `sched_get_priority_max()` return?
**Answer:** The maximum static priority for a given policy.

### 20. How does `SCHED_FIFO` differ from `SCHED_RR`?
**Answer:** `SCHED_RR` enforces a time quantum, whereas `SCHED_FIFO` runs a process until it yields or blocks.

### 21. What function gets current scheduling parameters?
**Answer:** `sched_getparam()`.

### 22. What function sets the priority of a process within its current policy?
**Answer:** `sched_setparam()`.

### 23. Can `SCHED_OTHER` processes have static priority?
**Answer:** No, static priority applies only to real-time policies.

### 24. What is the role of the time quantum in `SCHED_RR`?
**Answer:** It defines how long a process runs before being preempted.

### 25. How can you assign `SCHED_FIFO` to a process?
**Answer:** Use `sched_setscheduler()` with `SCHED_FIFO` policy and a priority.

### 26. What happens if two `SCHED_FIFO` tasks have the same priority?
**Answer:** They run in the order they become runnable and do not preempt each other.

### 27. How does Linux prevent starvation in real-time policies?
**Answer:** It does not guarantee prevention; poor real-time design can lead to starvation.

### 28. How to inspect a process’s scheduling policy?
**Answer:** Use `chrt -p <pid>` or `sched_getscheduler()`.

### 29. What is `chrt` command?
**Answer:** A CLI tool to manipulate or retrieve real-time scheduling attributes.

### 30. Can you use `nice()` with real-time policies?
**Answer:** No, nice values apply only to `SCHED_OTHER`.

---

## Practical Use Cases and Edge Cases

### 31. How to set a process to run with highest real-time priority?
**Answer:** Use `sched_setscheduler()` with `SCHED_FIFO` and priority 99.

### 32. What is the scheduling priority range for `SCHED_FIFO` and `SCHED_RR`?
**Answer:** 1 (lowest) to 99 (highest).

### 33. What is the behavior of `SCHED_OTHER` processes?
**Answer:** They are scheduled with dynamic priorities based on fairness.

### 34. What happens if a real-time task runs an infinite loop?
**Answer:** It can starve other processes unless preempted or it yields.

### 35. What happens if you assign an invalid priority?
**Answer:** `sched_setscheduler()` returns -1 and sets `errno` to `EINVAL`.

### 36. How to temporarily boost a process’s priority?
**Answer:** Change scheduling policy to real-time and set a higher priority.

### 37. What tools can inspect scheduling policy and priority?
**Answer:** `chrt`, `ps -eo pid,cls,rtprio,pri,ni,cmd`, `top`.

### 38. Can a parent process control its child’s scheduling?
**Answer:** Yes, by using `sched_setscheduler()` or `setpriority()`.

### 39. What causes `sched_setscheduler()` to fail?
**Answer:** Insufficient permissions, invalid PID, or bad priority value.

### 40. What kernel configuration enables real-time scheduling?
**Answer:** CONFIG_PREEMPT_RT or full preemption support.

---

## Deep Concepts

### 41. Can a `SCHED_RR` process be preempted by `SCHED_FIFO`?
**Answer:** Yes, if the FIFO process has a higher static priority.

### 42. How is real-time scheduling priority handled internally?
**Answer:** Through priority queues in the kernel scheduler.

### 43. Is `SCHED_OTHER` preemptive?
**Answer:** Yes, based on dynamic priority and CFS (Completely Fair Scheduler).

### 44. What determines task selection in `SCHED_OTHER`?
**Answer:** Fair share of CPU time via CFS's red-black tree.

### 45. How can starvation be mitigated in `SCHED_OTHER`?
**Answer:** Through dynamic priority adjustment.

### 46. How can starvation be mitigated in real-time scheduling?
**Answer:** Avoid poorly designed real-time loops and use watchdogs or yield.

### 47. What’s the behavior when mixing `SCHED_FIFO` and `SCHED_OTHER`?
**Answer:** `SCHED_FIFO` tasks always get preference.

### 48. How to programmatically yield CPU in real-time tasks?
**Answer:** Use `sched_yield()`.

### 49. What is the effect of calling `nice()` on a `SCHED_FIFO` task?
**Answer:** No effect, as nice values are ignored.

### 50. Can priority changes be inherited across fork?
**Answer:** Yes, a child inherits the scheduling parameters of the parent.

### 51. How to detect priority inversion?
**Answer:** Look for blocked high-priority tasks waiting on low-priority ones.

### 52. What is priority inheritance?
**Answer:** A protocol to avoid priority inversion by temporarily boosting the priority of a lower-priority task holding a resource.

---

*Prepared for Qualcomm/Linux system programming interviews — Topic: Scheduling and Priorities*

