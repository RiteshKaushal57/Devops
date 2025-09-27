**1. What is Linux and why is it used over Windows?**  
Linux is an open-source operating system based on Unix. It’s widely used in DevOps and servers because it’s lightweight, secure, and highly customizable. Compared to Windows, Linux is free, supports powerful command-line tools, and is the backbone of most cloud and container environments. That’s why most companies rely on Linux for scalability, automation, and reliability.

**2. Explain core components of linux machine.**  
A Linux machine is built on several core components. At the base is the **hardware**. On top of that sits the **kernel**, which manages resources like memory, CPU, and devices. Users interact with the system through the **shell**, which passes commands to the kernel. The system also provides **libraries and utilities** to support applications. Finally, at the top layer, we have **user applications** that we install and run. This layered structure makes Linux powerful, modular, and flexible.  

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