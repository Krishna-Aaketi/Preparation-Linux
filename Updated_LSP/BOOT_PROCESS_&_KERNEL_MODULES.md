# 🧩 BOOT PROCESS & KERNEL MODULES

*(Subsystem 9 of 10)*

---

### 1. What is the Linux boot process?

It’s the sequence of stages that occur from power-on to login prompt.

**Steps:**

1. **Firmware stage** – BIOS/UEFI initializes hardware.
2. **Bootloader stage** – loads kernel and initramfs.
3. **Kernel stage** – mounts root filesystem, starts PID 1.
4. **User space init stage** – systemd or init sets up services.

---

### 2. What does BIOS/UEFI do in booting?

Performs **POST (Power-On Self-Test)**, initializes CPU and memory, and locates the bootable device to load the bootloader into RAM.

---

### 3. What is a bootloader?

A small program that loads the Linux kernel image into memory and transfers control to it.
**Common bootloaders:** GRUB, LILO, U-Boot, SYSLINUX.

---

### 4. What are GRUB stages?

* **Stage 1:** stored in MBR, loads Stage 1.5.
* **Stage 1.5:** knows filesystem format.
* **Stage 2:** displays menu and loads kernel (vmlinuz) + initramfs.

---

### 5. What is the initramfs (initial RAM filesystem)?

A temporary root filesystem loaded into RAM before the actual root filesystem mounts.
Contains drivers, firmware, and early user-space tools.

---

### 6. What files does the bootloader load?

```
Kernel image   → /boot/vmlinuz-*
Initramfs image → /boot/initrd.img-*
Config          → /boot/config-*
System map      → /boot/System.map-*
```

---

### 7. What is the kernel’s role during boot?

* Decompresses itself
* Initializes hardware and memory
* Mounts initramfs
* Starts **PID 1 (usually systemd)**

---

### 8. What happens after the kernel starts PID 1?

`systemd` (or older `init`) executes unit files/scripts to:

* Mount filesystems
* Start daemons
* Enable networking
* Reach a usable target level

---

### 9. What is runlevel vs systemd target?

| Concept  | SysV-init          | systemd Equivalent                                   |
| -------- | ------------------ | ---------------------------------------------------- |
| Runlevel | 0–6 numeric stages | Targets like `multi-user.target`, `graphical.target` |

---

### 10. What are typical boot stages (summary)?

1. Power-on
2. BIOS/UEFI
3. Bootloader (GRUB)
4. Kernel load
5. Initramfs
6. Root mount
7. Systemd/init
8. Login shell

---

### 11. What is kernel initialization sequence?

Starts from `start_kernel()` → sets up scheduler, memory management, interrupts, drivers → mounts rootfs → spawns PID 1.

---

### 12. What is the difference between `/boot` and `/`?

| Directory | Purpose                                         |
| --------- | ----------------------------------------------- |
| **/boot** | Holds static kernel/bootloader files            |
| **/**     | The mounted root filesystem for full OS runtime |

---

### 13. What is a kernel image (vmlinuz)?

A compressed, executable image of the Linux kernel (`z` = gzip compression).

---

### 14. What is a device tree blob (.dtb)?

Binary hardware description (used mainly on ARM) telling the kernel what devices exist and their addresses.

---

### 15. What is the kernel command line?

Parameters passed by the bootloader (in GRUB’s `linux …` line) to configure kernel features, e.g.:

```
root=/dev/sda1 quiet nosplash
```

---

### 16. What are initrd and initramfs differences?

| Feature      | initrd                             | initramfs                      |
| ------------ | ---------------------------------- | ------------------------------ |
| Type         | Block image mounted as loop device | CPIO archive unpacked into RAM |
| Modern       | Legacy                             | Modern                         |
| Mount Method | As device                          | In-memory filesystem           |

---

### 17. What is the purpose of the root filesystem mount?

After initramfs, kernel mounts the actual root (`/`) filesystem and switches execution to `/sbin/init`.

---

### 18. What are kernel modules?

Loadable pieces of code (`.ko` files) that add functionality (e.g., drivers, filesystems) without recompiling or rebooting the kernel.

---

### 19. What are the advantages of loadable modules?

* On-demand loading/unloading
* Smaller kernel footprint
* Simplified debugging and updates

---

### 20. What command lists loaded modules?

```
lsmod
cat /proc/modules
```

---

### 21. How to load and unload a module?

```
Load:   insmod module.ko   OR   modprobe module
Unload: rmmod module       OR   modprobe -r module
```

---

### 22. What is the difference between insmod and modprobe?

* **insmod** → loads a module directly.
* **modprobe** → resolves and loads dependencies automatically from `/lib/modules/$(uname -r)`.

---

### 23. What is depmod?

Utility that scans the module directory and builds **modules.dep** dependency map used by `modprobe`.

---

### 24. How does Linux auto-load modules?

Through **udev events** or kernel **autofs requests** when new hardware appears.

---

### 25. What are kernel module init/exit macros?

```c
module_init(driver_init);
module_exit(driver_exit);
```

These register the functions that run when the module loads/unloads.

---

### 26. What information is stored in a module header?

Metadata like name, license, author, version, and dependencies, declared using:

```c
MODULE_LICENSE("GPL");
MODULE_AUTHOR("Hari");
MODULE_DESCRIPTION("My driver");
```

---

### 27. What is modinfo used for?

Displays module metadata such as filename, author, license, parameters, and dependencies.

---

### 28. What are module parameters?

Variables adjustable at load time via:

```
modprobe module param=value
```

Declared with `module_param()` macros.

---

### 29. Where are modules stored?

```
/lib/modules/<kernel-version>/kernel/
```

Organized by subsystem (drivers, fs, net, etc.).

---

### 30. What is a built-in vs loadable module?

| Type         | Description          | Kernel Config |
| ------------ | -------------------- | ------------- |
| **Built-in** | Compiled into kernel | `y`           |
| **Loadable** | Separate `.ko` file  | `m`           |

---

### 31. What is `uname -r` used for?

Displays the running kernel version — used to locate its module directory.

---

### 32. What is the kernel version numbering scheme?

```
<major>.<minor>.<patch>  →  e.g., 6.8.12
```

* Major = big feature
* Minor = stable updates
* Patch = fixes

---

### 33. What is kernel configuration file (.config)?

Defines enabled features and modules; generated by:

```
make menuconfig
make defconfig
```

---

### 34. What is kernel compilation workflow?

```
make menuconfig
make -j$(nproc)
make modules_install
make install
update-grub
reboot
```

---

### 35. What are kernel log messages used for debugging boot?

`dmesg` displays the **kernel ring buffer**, useful to verify driver loads and boot events.

---

### 36. What are initcall levels?

Kernel organizes initialization routines by priority (`core_initcall`, `module_init`, etc.) executed in defined order during boot.

---

### 37. What is kexec?

A mechanism to load and boot into a new kernel from a running system, skipping firmware and bootloader stages — **fast reboots or crash dumping**.

---

### 38. What is crashkernel / kdump?

Crash-recovery subsystem using a reserved memory region to boot a minimal kernel and capture memory dump after a crash.

---

### 39. What is Secure Boot?

UEFI feature ensuring only **signed bootloaders and kernels** are executed — prevents unauthorized code at boot.

---

### 40. What are kernel boot parameters?

Options appended by bootloader to tune features, e.g.:

```
init=/bin/bash single acpi=off quiet loglevel=7
```

---

### 41. What is systemd-boot vs GRUB?

| Boot Manager     | Description                                             |
| ---------------- | ------------------------------------------------------- |
| **systemd-boot** | Simple UEFI boot manager integrated with systemd        |
| **GRUB**         | Feature-rich, supports legacy BIOS and more filesystems |

---

### 42. What is init process PID 1 significance?

The **first user-space process**; if PID 1 terminates, the kernel panics (`no init found`).

---

### 43. What are systemd units?

Configuration files describing services, sockets, mounts, timers, etc.
Located under:

```
/etc/systemd/system
/usr/lib/systemd/system
```

---

### 44. How to view boot logs in systemd systems?

```
journalctl -b
```

Shows all kernel and user-space logs since the last boot.

---

### 45. What is the purpose of the kernel ring buffer?

Stores early boot and driver messages before logging daemons start.
Accessed via:

```
dmesg
```

---

### 46. What is an initramfs hook?

A script run during initramfs generation (`/etc/initramfs-tools/hooks`) to include drivers or firmware needed before root mount.

---

### 47. What is a kernel panic?

Critical failure where kernel halts because it can’t continue safely; often shows a **stack trace** on console.

---

### 48. What is soft vs hard reboot?

| Type            | Description                                |
| --------------- | ------------------------------------------ |
| **Soft Reboot** | Triggered via software (`reboot`, `kexec`) |
| **Hard Reboot** | Physical reset or power cycle              |

---

### 49. What is firmware vs driver?

| Concept      | Runs On   | Description                           |
| ------------ | --------- | ------------------------------------- |
| **Firmware** | Device    | Microcode on hardware                 |
| **Driver**   | OS Kernel | Controls and interfaces with firmware |

---

### 50. What is the full Linux boot sequence summary?

1. **Power** → BIOS/UEFI initializes hardware
2. **Bootloader** loads kernel + initramfs
3. **Kernel** uncompresses, sets up MMU, starts drivers
4. **Root filesystem** mounted
5. **PID 1 (systemd)** starts services
6. **User login shell** launched

---
