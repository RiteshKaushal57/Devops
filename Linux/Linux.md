## 1. What is Linux and why is it used over Windows?  
Linux is an open-source operating system based on Unix. It’s widely used in DevOps and servers because it’s lightweight, secure, and highly customizable. Compared to Windows, Linux is free, supports powerful command-line tools, and is the backbone of most cloud and container environments. That’s why most companies rely on Linux for scalability, automation, and reliability.

## 2. Explain core components of linux machine.  
A Linux machine is built on several core components. At the base is the **hardware**. On top of that sits the **kernel**, which manages resources like memory, CPU, and devices. Linux interacts with hardware through the kernel. Users interact with the system through the **shell**, which passes commands to the kernel. The system also provides **libraries and utilities** to support applications. Finally, at the top layer, we have **user applications** that we install and run. This layered structure makes Linux powerful, modular, and flexible.    

+----------------------------------------------------+  
| User Applications (Vim, Docker, Apache, etc.)     |  
+----------------------------------------------------+  
| Shell (Bash, Zsh, Fish, etc.)                     |  <-- Part of the OS  
+----------------------------------------------------+  
| System Libraries (glibc, libc, OpenSSL, etc.)     |  <-- Part of the OS  
+----------------------------------------------------+  
| System Utilities (ls, grep, systemctl, etc.)      |  <-- Part of the OS  
+----------------------------------------------------+  
| Linux Kernel (Process, Memory, FS, Network)       |  <-- Core of the OS  
+----------------------------------------------------+  
| Hardware (CPU, RAM, Disk, Network, Peripherals)   |  
+----------------------------------------------------+      


## 3. What are Linux Distributions?  
Linux distributions (distros) are different versions of Linux that package the Linux kernel with various software, system utilities, and package managers. Each distro is designed for different use cases, such as personal computing, server management, or security.  

Here are some popular Linux distributions:  

**Ubuntu** – One of the most beginner-friendly distros, widely used for personal and server use. It has great community support.  

**CentOS** (discontinued, replaced by AlmaLinux/Rocky Linux) – Previously a popular choice for servers, based on Red Hat Enterprise Linux (RHEL).  

**Debian** – A very stable and reliable distro, often used as a base for other distros like Ubuntu.  

**Fedora** – A cutting-edge distro that introduces new features before they reach RHEL.   

**Arch Linux** – A lightweight, rolling-release distro for advanced users who like customization.   

**Kali Linux** – Designed for cybersecurity and penetration testing.   

**Alpine Linux** – A lightweight, security-focused distro often used in containers.     


## Linux Directory Definitions

**/sbin**  
System binaries used to manage the system as an administrator. Only root or users with sudo privileges can execute these commands.  
**/lib**  
Contains shared libraries used by the kernel and system programs. These are generally not used directly by regular users.  
**/boot**  
Contains files required for booting the system, such as the kernel, initial RAM disk image, and bootloader configuration.  
**/bin**  
User binaries. These are essential commands that can be used by all users. They are not limited to system administration.  

> **Note:** As a Linux admin, you can restrict `/sbin` commands to administrators, whereas `/bin` commands can be accessible to every user.  

**/srv**  
Stores data for services provided by the system, such as web server files or FTP files.  
**/opt**  
Optional software packages or third-party applications. Useful in organizations or teams to keep additional software separate from system files.  
**/mnt**  
Used by administrators to temporarily mount filesystems or external drives.  
**/media**  
Used for mounting removable media such as USB drives, CDs, and DVDs.  
**/var**  
Contains variable data files, such as logs, spool files, caches, and temporary files.  
**/data**  
A folder for sharing information among users. Access can be restricted based on permissions.  

## User Management  

- **To create new user without a home directory:** 
```
useradd username
```

- **To create new user with home directory:** 
```
useradd -m username
```