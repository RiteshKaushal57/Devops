**1. What is Docker?**  
Docker is an open-source platform that lets us package applications and all their dependencies into containers. These containers are lightweight, portable, and run the same way across different environments. It’s mainly used to avoid environment issues, speed up deployments, and make applications more scalable and efficient compared to traditional virtual machines.  

**2. What are virtual machines?**  
A Virtual Machine is a software-based computer that runs its own operating system and apps on top of a physical machine. It’s managed by a hypervisor and provides isolation, making it useful for running multiple OSes or testing in different environments.  

**3. What is difference between VM and Docker?**  
Containers and virtual machines are both technologies used to isolate applications and their dependencies, but they have some key differences:  
1. Resource Utilization: Containers share the host operating system kernel, making them lighter and faster than VMs. VMs have a full-fledged OS and hypervisor, making them more resource-intensive.  
2. Portability: Containers are designed to be portable and can run on any system with a compatible host operating system. VMs are less portable as they need a compatible hypervisor to run.  
3. Security: VMs provide a higher level of security as each VM has its own operating system and can be isolated from the host and other VMs. Containers provide less isolation, as they share the host operating system.  
4. Management: Managing containers is typically easier than managing VMs, as containers are designed to be lightweight and fast-moving. 

**4. What is a hypervisor?**  
A hypervisor is software that creates and manages virtual machines by allocating hardware resources to them. It allows multiple operating systems to run on one physical machine.  

**5. What is Docker Architecture?**   
Docker Architecture is a client-server-based architecture that defines how Docker components interact to build, ship, and run containerized applications. It includes the **Docker Client**, **Docker Daemon (Engine)**, **Docker Objects (Images, Containers)**, and **Registries**.   
![Alt text](./image.png)  

**Docker Client:** CLI or API used to communicate with Docker Daemon (docker build, docker run).  

**Docker Daemon (Docker Engine):** Runs on the host and manages images, containers, networks, and storage.  

**Docker Images:** Read-only templates used to create containers.  

**Docker Containers:** Running instances of images, providing isolated runtime environments.  

**Dockerfile:** Dockerfile is a file where you provide the steps to build your Docker Image.  

**Docker Registries:** Storage for images (like Docker Hub Docker Hub (*place where you share your docker images with external hub*) or private registries).  

*Orchestration Layer:* Tools like Kubernetes or Docker Swarm for managing multiple containers.  

**Docker LifeCycle**  
**docker build ->** builds docker images from Dockerfile  
**docker run ->** runs container from docker images  
**docker push ->** push the container image to public/private regestries to share the docker images.     

**5. What is Docker Container?**  
A Docker container is a lightweight, standalone package that includes an application and all its dependencies. It runs in an isolated environment, ensuring consistency across different systems, is faster and more resource-efficient than virtual machines, and is widely used for scalable and microservices-based deployments.   

**6. Why are containers light weight?**   
Containers are lightweight because they use a technology called containerization, which allows them to share the host operating system's kernel and libraries, while still providing isolation for the application and its dependencies. This results in a smaller footprint compared to traditional virtual machines, as the containers do not need to include a full operating system. Additionally, Docker containers are designed to be minimal, only including what is necessary for the application to run, further reducing their size.   

**7. What is the architecture of containers?**  

+-----------------------+  
|   Container (App)     | *Lightweight, isolated runtime environments for apps.*   
+-----------------------+  
|   Docker Image Layer  | *Read-only templates used to create containers.*    
+-----------------------+  
|   Docker Engine       | *Core software that runs containers (builds images, manages containers, handles networking).*    
+-----------------------+  
|   Host OS & Kernel    | *Provides system resources like CPU, memory, and I/O.*   
+-----------------------+  
|   Physical Hardware   |    
+-----------------------+    

The architecture of containers is layered. The Docker Engine runs on the host OS and manages containers. Each container is created from a Docker image, providing an isolated environment for the app while sharing the host OS kernel. Networking and volumes allow communication and persistent storage. For multiple containers, orchestration tools like Kubernetes handle scaling, load balancing, and monitoring.  

**Files and Folders in containers base images**
    /bin: contains binary executable files, such as the ls, cp, and ps commands.  

    /sbin: contains system binary executable files, such as the init and shutdown commands.  

    /etc: contains configuration files for various system services.  

    /lib: contains library files that are used by the binary executables.  

    /usr: contains user-related files and utilities, such as applications, libraries, and documentation.   

    /var: contains variable data, such as log files, spool files, and temporary files.   

    /root: is the home directory of the root user.  


**Files and Folders that containers use from host operating system**
    The host's file system: Docker containers can access the host file system using bind mounts, which allow the container to read and write files in the host file system.  

    Networking stack: The host's networking stack is used to provide network connectivity to the container. Docker containers can be connected to the host's network directly or through a virtual network.   

    System calls: The host's kernel handles system calls from the container, which is how the container accesses the host's resources, such as CPU, memory, and I/O.   
 
    Namespaces: Docker containers use Linux namespaces to create isolated environments for the container's processes. Namespaces provide isolation for resources such as the file system, process ID, and network.   

    Control groups (cgroups): Docker containers use cgroups to limit and control the amount of resources, such as CPU, memory, and I/O, that a container can access.   


     