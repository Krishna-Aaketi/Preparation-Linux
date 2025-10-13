# 🔐 SYSTEM SECURITY & USER MANAGEMENT

#### (Subsystem 10 of 10)

---

### **1. What is system security in Linux?**

System security means protecting data, processes, and system resources from unauthorized access or modification by enforcing authentication, permissions, and auditing.

---

### **2. What are the three basic pillars of Linux security?**

1. **Authentication** – verifying user identity.
2. **Authorization** – controlling access rights.
3. **Accounting/Auditing** – tracking user actions.

---

### **3. What is the role of the root user?**

The **root** account (UID 0) has unrestricted access to all system resources.  Misuse can damage the system; administrators often prefer `sudo` for safer privilege escalation.

---

### **4. What are UID and GID?**

* **UID (User ID):** identifies a user.
* **GID (Group ID):** identifies the primary group a user belongs to.
  Defined in `/etc/passwd` and `/etc/group`.

---

### **5. What is the difference between real UID and effective UID?**

* **Real UID:** the user who started the process.
* **Effective UID:** the identity the process is *currently* running as (used for permission checks).
  `setuid` binaries can change effective UID temporarily.

---

### **6. What is a set-UID (SUID) program?**

A program that runs with the privileges of its file owner rather than the user executing it — for example `/usr/bin/passwd`.

---

### **7. What is set-GID (SGID)?**

Similar to SUID but applies group privileges.
On directories, files created inside inherit the directory’s group ID.

---

### **8. What is the sticky bit?**

On directories, it ensures only the file owner can delete or rename files (e.g. `/tmp`).

---

### **9. What are file permission bits?**

Nine bits: `rwx rwx rwx` for *owner | group | others*.
Plus three special bits (SUID, SGID, sticky).
Change with `chmod`, `chown`, `chgrp`.

---

### **10. How to view user and group IDs?**

`id <username>` or simply `id`.

---

### **11. What are system vs normal users?**

* **System users** (UID < 1000) → created for services.
* **Normal users** (UID ≥ 1000) → interactive logins.

---

### **12. How to create, modify, delete users?**

* `useradd`, `usermod`, `userdel`.
* For groups: `groupadd`, `groupdel`.

---

### **13. What is `/etc/passwd` used for?**

Stores username, UID, GID, home dir, and shell; passwords are shadowed in `/etc/shadow`.

---

### **14. What is `/etc/shadow`?**

Contains encrypted passwords and expiry information; readable only by root.

---

### **15. What hashing algorithms protect passwords?**

SHA-512 (default), MD5, bcrypt, yescrypt — stored with salt.

---

### **16. What is PAM (Pluggable Authentication Modules)?**

Framework providing modular authentication.
Config files: `/etc/pam.d/`, controlling login, sudo, SSH, etc.

---

### **17. What is `sudo` and why use it?**

`sudo` executes commands as another user (typically root) per rules in `/etc/sudoers`.
Reduces risk by avoiding full root logins.

---

### **18. What is `/etc/sudoers`?**

File defining who can run what as whom, edited safely with `visudo`.

---

### **19. What are Linux capabilities?**

Fine-grained privileges splitting root powers into units (e.g., `CAP_NET_ADMIN`, `CAP_SYS_BOOT`).
Assigned with `setcap`, viewed via `getcap`.

---

### **20. What is discretionary vs mandatory access control?**

| Type    | Control Source            | Example           |
| ------- | ------------------------- | ----------------- |
| **DAC** | Owner decides permissions | chmod/chown       |
| **MAC** | System-enforced policy    | SELinux, AppArmor |

---

### **21. What is SELinux?**

(Security-Enhanced Linux) A MAC system enforcing policies based on labels — every process and file has a *security context* (`user:role:type:level`).

---

### **22. What are SELinux modes?**

* **Enforcing** → policy applied.
* **Permissive** → policy logged only.
* **Disabled** → inactive.
  View with `getenforce`, change with `setenforce`.

---

### **23. What is AppArmor?**

An alternative MAC framework using *path-based profiles* to confine programs.
Config: `/etc/apparmor.d/`.

---

### **24. What are Linux namespaces used for security?**

Provide isolation of resources (PID, NET, MNT, IPC, USER, UTS) — foundation for containers.

---

### **25. What is a chroot jail?**

Limits a process to a subtree of the filesystem, making it appear as `/`.  Used for sandboxing or recovery environments.

---

### **26. What is `seccomp`?**

Secure Computing mode; restricts system calls available to a process.
Configured via `seccomp()` or containers’ profiles.

---

### **27. What are control groups (cgroups)?**

Kernel mechanism to limit and monitor resource usage (CPU, memory, I/O, network) per group of processes.

---

### **28. What is auditd?**

Linux auditing daemon logging security-relevant events — configured via `/etc/audit/audit.rules`.

---

### **29. What is `/var/log/auth.log`?**

Stores authentication and authorization logs (logins, sudo, SSH).

---

### **30. What is fail2ban?**

Monitors logs for repeated failed logins and temporarily bans offending IPs via firewall rules.

---

### **31. What is the difference between symmetric and asymmetric encryption?**

* **Symmetric:** same key for encrypt/decrypt (AES).
* **Asymmetric:** public/private key pair (RSA, ECDSA).

---

### **32. What is GPG?**

GNU Privacy Guard — tool for encrypting/signing data using OpenPGP keys (`gpg --gen-key`, `gpg -e`).

---

### **33. What is SSH?**

Secure Shell protocol for encrypted remote login and file transfer (SCP/SFTP).

---

### **34. What are SSH key pairs?**

Public/private keys (~/.ssh/id_rsa and ~/.ssh/id_rsa.pub) enabling password-less yet secure authentication.

---

### **35. What is a firewall?**

A system that filters inbound/outbound network packets based on rules.
Managed by `iptables`, `nftables`, or `ufw`.

---

### **36. What is the Uncomplicated Firewall (UFW)?**

Front-end for iptables simplifying rule management.
Commands: `ufw enable`, `ufw allow 22`, `ufw status`.

---

### **37. What is the Linux security module (LSM) framework?**

Kernel API layer allowing multiple security systems (SELinux, AppArmor, Smack, TOMOYO) to coexist.

---

### **38. What is password aging?**

System-enforced password expiry policy set via `chage`, stored in `/etc/shadow`.

---

### **39. What is two-factor authentication (2FA)?**

Adds a second verification step (OTP, token) in addition to password — supported through PAM modules like `google-pam`.

---

### **40. What is the difference between `/etc/login.defs` and `/etc/security/limits.conf`?**

* `login.defs` → default password/UID policies.
* `limits.conf` → per-user resource limits (CPU, memory, open files).

---

### **41. What is `passwd -l` vs `passwd -u`?**

Lock (`-l`) or unlock (`-u`) a user’s password (disabling/enabling login).

---

### **42. What is SSH hardening?**

Disable root login, use key-based auth, change default port, limit users, enable fail2ban.

---

### **43. What is kernel lockdown mode?**

Restricts kernel modifications and low-level access even by root when Secure Boot is active — protects kernel integrity.

---

### **44. What is integrity measurement architecture (IMA)?**

Framework that measures and optionally appraises files and binaries before execution to ensure they are unmodified.

---

### **45. What is disk encryption in Linux?**

Protects data at rest using LUKS/dm-crypt (`cryptsetup luksFormat`).

---

### **46. What is a TPM?**

Trusted Platform Module — hardware chip providing secure key storage and measured boot.

---

### **47. What are the roles of `/etc/hosts.allow` and `/etc/hosts.deny`?**

Legacy TCP Wrappers access control: defines which hosts can or cannot use network services.

---

### **48. What is the difference between physical and logical security?**

* **Physical:** hardware access control (locks, BIOS passwords).
* **Logical:** software controls (permissions, firewall, encryption).

---

### **49. How to check active logins and sessions?**

`who`, `w`, `last`, `users`, `loginctl list-sessions`.

---

### **50. What are best practices for securing a Linux system?**

* Keep system updated (`apt update && upgrade`).
* Use least privilege principle.
* Enforce strong passwords & 2FA.
* Enable firewall & audit logs.
* Disable unused services.
* Regularly monitor `/var/log/`.
* Use SELinux/AppArmor for confinement.
* Encrypt sensitive data.

---
