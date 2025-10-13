# ⚙️ Device Drivers & Hardware Interface  
### Subsystem 7 of 10

---

## 1️⃣ What is a Device Driver?
A **device driver** is kernel code that acts as a bridge between hardware and the OS, exposing devices as **file-like interfaces** to user space.

---

## 2️⃣ Why Are Drivers Kept in Kernel Space?
Because drivers require **direct access** to:
- Hardware registers  
- Interrupts  
- DMA (Direct Memory Access)  

Such operations aren’t permitted in user mode for **security and stability**.

---

## 3️⃣ Main Types of Device Drivers
- **Character drivers** – byte-stream I/O (serial ports, sensors)  
- **Block drivers** – block-oriented I/O (disks, SSDs)  
- **Network drivers** – packet-based communication  
- **Misc / Pseudo drivers** – logical or virtual devices  

---

## 4️⃣ Character vs Block Drivers
| Feature | Character Driver | Block Driver |
|:--|:--|:--|
| I/O Type | Byte stream | Fixed-size blocks |
| Buffering | No page cache | Uses buffer/page cache |
| Example | `/dev/ttyS0` | `/dev/sda` |
| Interface | read/write/ioctl | Filesystem layer |

---

## 5️⃣ Major & Minor Numbers
Each device file under `/dev` has:
- **Major number** → identifies the driver  
- **Minor number** → identifies specific device instance  

---

## 6️⃣ Device Files Location
Located under **`/dev`**, created:
- Automatically by **udev**
- Manually via `mknod`

---

## 7️⃣ udev Daemon
A **user-space service** that dynamically creates and removes `/dev` device files when hardware is connected or removed.

---

## 8️⃣ Kernel’s Device Model
Unifies representation of **devices, drivers, and buses** in `sysfs` (`/sys`), enabling:
- Hot-plugging  
- Power management  
- Unified control interface  

---

## 9️⃣ Bus Drivers in Linux
Bus drivers (PCI, USB, I²C, SPI, etc.) detect devices and call their **probe()** functions for initialization.

---

## 🔟 Kernel Module
A **loadable code object** (`.ko`) that extends kernel functionality at runtime using `insmod` or `modprobe`.

---

## 11️⃣ Advantages of Loadable Kernel Modules (LKM)
- Add/remove features **without rebooting**  
- Easier **development and debugging**  
- Reduced **kernel size and memory footprint**

---

## 12️⃣ Key Module Functions
```c
module_init(my_init);
module_exit(my_exit);
````

Run when the module is loaded or unloaded.

---

## 13️⃣ Module Metadata Macros

```c
MODULE_LICENSE("GPL");
MODULE_AUTHOR("Hari");
MODULE_DESCRIPTION("Example driver");
```

Shown via `modinfo`.

---

## 14️⃣ View Loaded Kernel Modules

```bash
lsmod
cat /proc/modules
```

---

## 15️⃣ Insert / Remove Modules

* **Insert:** `insmod mydriver.ko` or `modprobe mydriver`
* **Remove:** `rmmod mydriver` or `modprobe -r mydriver`

---

## 16️⃣ /proc/devices

Lists **registered character and block drivers** with their major numbers.

---

## 17️⃣ Key File Operations for Character Drivers

`open()`, `release()`, `read()`, `write()`, `ioctl()`, `llseek()` — all defined in **struct file_operations**.

---

## 18️⃣ register_chrdev_region()

Reserves a range of **device numbers** for a new character driver.

---

## 19️⃣ cdev Structure

Represents a character device within the kernel.
Links **file operations** with **VFS (Virtual File System)**.

---

## 20️⃣ Probe / Remove Mechanism

When a device is detected:

* **probe()** → called for initialization
  When removed:
* **remove()** → cleans up resources

---

## 21️⃣ Interrupt

A **hardware signal** notifying CPU of an event (e.g., data ready).

---

## 22️⃣ Types of Interrupts

* **Hardware interrupts** – from external devices
* **Software interrupts** – from traps/syscalls
* **Exceptions** – CPU-detected faults (e.g., divide-by-zero)

---

## 23️⃣ Interrupt Handler (ISR)

Kernel routine registered using:

```c
request_irq(irq, handler, flags, name, dev);
```

Executes in **interrupt context** to handle events.

---

## 24️⃣ Top Half vs Bottom Half

| Type            | Role                                        |
| :-------------- | :------------------------------------------ |
| **Top Half**    | Immediate ISR — minimal, time-critical      |
| **Bottom Half** | Deferred work (softirq, tasklet, workqueue) |

---

## 25️⃣ SoftIRQ, Tasklet, and Workqueue Comparison

| Mechanism     | Context   | Sleep? | Use Case                  |
| :------------ | :-------- | :----- | :------------------------ |
| **SoftIRQ**   | Interrupt | No     | High-frequency (network)  |
| **Tasklet**   | Interrupt | No     | Lightweight deferred work |
| **Workqueue** | Process   | Yes    | Long tasks, can block     |

---

## 26️⃣ DMA (Direct Memory Access)

Allows **devices to transfer data directly** to memory without CPU, improving throughput.

---

## 27️⃣ DMA Initialization

Driver allocates DMA buffer:

```c
dma_alloc_coherent(dev, size, &dma_handle, GFP_KERNEL);
```

Then programs device DMA registers with physical addresses.

---

## 28️⃣ Kernel I/O Memory Access APIs

* `ioremap()` / `iounmap()` → map device registers
* `readb()`, `writeb()`, `readl()`, `writel()` → access mapped registers

---

## 29️⃣ Polling vs Interrupt-Driven I/O

| Type                 | Description                                      |
| :------------------- | :----------------------------------------------- |
| **Polling**          | CPU repeatedly checks device status              |
| **Interrupt-driven** | Device signals CPU when ready *(more efficient)* |

---

## 30️⃣ Memory-Mapped I/O

Device registers mapped into **system address space** — accessed like normal memory.

---

## 31️⃣ Port-Mapped I/O

Legacy approach using **dedicated I/O ports** via instructions `inb`, `outb`.

---

## 32️⃣ Platform Driver

Framework for **on-board (non-discoverable)** devices defined via **Device Tree** or **ACPI**.

---

## 33️⃣ Device Tree

A hardware description (`.dts`) listing SoC peripherals, addresses, interrupts, and configuration for embedded systems.

---

## 34️⃣ of_match_table

Table mapping **Device Tree compatible strings** to driver probe functions.

---

## 35️⃣ Hot-Plug vs Plug-and-Play

| Type              | Description                                     |
| :---------------- | :---------------------------------------------- |
| **Hot-plug**      | Device attach/detach while system running (USB) |
| **Plug-and-play** | Auto-detection/configuration at boot            |

---

## 36️⃣ Power Management in Drivers

Drivers implement:

```c
suspend() / resume()
```

in **pm_ops** to manage power when idle.

---

## 37️⃣ Driver Debugging Tools

* `dmesg`, `printk()`
* `/proc/interrupts`, `/sys/kernel/debug`
* `trace_printk()`, `ftrace`, `perf`

---

## 38️⃣ printk Log Levels

`KERN_EMERG`, `ALERT`, `CRIT`, `ERR`, `WARNING`, `NOTICE`, `INFO`, `DEBUG`

---

## 39️⃣ kobject & kset

Core kernel objects representing hierarchy in `/sys`; all devices & drivers have **kobjects**.

---

## 40️⃣ sysfs

Virtual filesystem exposing:

* `/sys/class`
* `/sys/bus`
* `/sys/devices`

Used for user-space interaction with kernel objects.

---

## 41️⃣ dev_dbg() vs printk()

`dev_dbg()` ties messages to a **specific device**, toggleable at runtime;
`printk()` is global.

---

## 42️⃣ ioctl() in Drivers

Handles **device-specific control commands**:

```c
unlocked_ioctl(struct file *file, unsigned int cmd, unsigned long arg)
```

User: `ioctl(fd, cmd, arg)` → Driver: handles operation.

---

## 43️⃣ Blocking vs Non-Blocking Operations

| Mode             | Behavior                                            |
| :--------------- | :-------------------------------------------------- |
| **Blocking**     | Process sleeps until data is ready (`wait_event()`) |
| **Non-blocking** | Returns immediately with `-EAGAIN`                  |

---

## 44️⃣ Wait Queue

Kernel structure to **suspend processes** until a condition is met, resumed using `wake_up()`.

---

## 45️⃣ Kernel Thread (kthread)

Thread running **entirely in kernel space**.
Created using:

```c
kthread_run(fn, data, "thread_name");
```

---

## 46️⃣ IRQ Sharing

Multiple devices share the same interrupt line.
Driver must specify `IRQF_SHARED` in `request_irq()`.

---

## 47️⃣ free_irq()

Frees an interrupt line when device is removed or driver unloaded.

---

## 48️⃣ Kernel Timer

Deferred task mechanism initialized with:

```c
timer_setup(&timer, handler, 0);
mod_timer(&timer, jiffies + delay);
```

---

## 49️⃣ High-Resolution Timer (hrtimer)

Provides **nanosecond precision** for real-time or multimedia applications.

---

## 50️⃣ Kernel Device Driver Life Cycle

1. Device detected
2. Bus match → Driver **probe()**
3. Initialization & registration
4. Runtime I/O, interrupts, power mgmt
5. Removal → **remove()** cleanup

---

## ✅ Summary

* Drivers connect hardware to OS via **kernel interfaces**.
* Support for **interrupts, DMA, sysfs, power management**.
* Tools: `dmesg`, `ftrace`, `perf`, `/sys`.
* Modular design via **LKM** enables runtime flexibility.

---
```
