## 1. What is Docker?  
Docker is an open-source platform that lets us package applications and all their dependencies into containers. These containers are lightweight, portable, and run the same way across different environments. It’s mainly used to avoid environment issues, speed up deployments, and make applications more scalable and efficient compared to traditional virtual machines.  

## 2. What are virtual machines?  
A Virtual Machine is a software-based computer that runs its own operating system and apps on top of a physical machine. It’s managed by a hypervisor and provides isolation, making it useful for running multiple OSes or testing in different environments.  

## 3. What is difference between VM and Docker?  
Docker and virtual machines are both technologies used to isolate applications and their dependencies, but they have some key differences:  
**1. Resource Utilization:** Containers share the host operating system kernel, making them lighter and faster than VMs. VMs have a full-fledged OS and hypervisor, making them more resource-intensive.  
**2. Portability:** Containers are designed to be portable and can run on any system with a compatible host operating system. VMs are less portable as they need a compatible hypervisor to run.  
**3. Security:** VMs provide a higher level of security as each VM has its own operating system and can be isolated from the host and other VMs. Containers provide less isolation, as they share the host operating system.  
**4. Management:** Managing containers is typically easier than managing VMs, as containers are designed to be lightweight and fast-moving. 

## 4. What is a hypervisor?  
A hypervisor is software that creates and manages virtual machines by allocating hardware resources to them. It allows multiple operating systems to run on one physical machine.  

## 5. What is Docker Architecture?   
Docker Architecture is a client-server-based architecture that defines how Docker components interact to build, ship, and run containerized applications. It includes the **Docker Client**, **Docker Daemon (Engine)**, **Docker Objects (Images, Containers)**, and **Registries**.   
![Alt text](image.png)  

**Docker Client:** CLI or API used to communicate with Docker Daemon (docker build, docker run).  

**Docker Daemon (Docker Engine):** Runs on the host and manages images, containers, networks, and storage.  

**Docker Images:** Read-only templates used to create containers. A Docker image is like a blueprint of an application and everything it needs to run—code, libraries, dependencies, environment variables.   

**Docker Containers:** A container is a running instance of an image. The image itself is static—it’s just the blueprint with your app and dependencies. The container is like a “live copy” of that blueprint that can actually execute code, hold temporary data, and interact with the system. When you run a container, Docker takes the image, sets up a thin writable layer on top, and starts executing it. This writable layer is where all changes inside the container happen (like creating files, updating data, or installing something temporarily). The image underneath remains unchanged, so you can always create new containers from the same image, and they’ll start fresh every time.      

**Dockerfile:** A Dockerfile is a text file that contains commands or instructions to build a Docker image. It can specify the base image, the files to add to the container, the commands to run during the build process, and the command to run when the container starts.

**Docker Registries:** Storage for images (like Docker Hub Docker Hub (*place where you share your docker images with external hub*) or private registries).  

*Orchestration Layer:* Tools like Kubernetes or Docker Swarm for managing multiple containers.  

### Docker LifeCycle  
- **docker build ->** builds docker images from Dockerfile  
- **docker run ->** runs container from docker images  
- **docker push ->** push the container image to public/private regestries to share the docker images.     

## 5. What is Docker Container?  
A Docker container is a lightweight, standalone package that includes an application and all its dependencies. It runs in an isolated environment, ensuring consistency across different systems, is faster and more resource-efficient than virtual machines, and is widely used for scalable and microservices-based deployments.   

## 6. Why are containers light weight?   
Containers are lightweight because they share the main operating system of your computer instead of carrying a full OS like a virtual machine does. Each container still keeps your app and its dependencies separated and safe from other apps. This makes containers much smaller and faster. On top of that, Docker containers only include what your app actually needs to run, which keeps their size minimal.   

## 7. What is the architecture of containers?  

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


## 8. What is a Multi-Stage Docker Build?  
A Multi-Stage Docker Build is a way to create Docker images in multiple steps within one Dockerfile.

The main reason we use multi-stage builds is to reduce the final image size and make it more efficient. Normally, when you build an app inside Docker, the image includes all the build tools and temporary files, even though they aren’t needed in production. This makes the image large, slow, and less secure. Multi-stage builds solve this by using one stage for building and another for running, and only copying the required files from the builder to the final stage.


**Distroless Image:**   
Distroless images are special Docker images that contain only the files needed to run your app, nothing extra — no shell, no package manager, no tools. We use them because they make our applications more secure, lighter, and faster. When an image has less stuff inside it, there is less for hackers to attack, less space taken on servers, and less time spent pulling or deploying it. They also help keep production environments clean, because only the final application code goes inside the container. We use distroless images by first building our full app in a normal image (using a multi-stage build) and then copying only the final output into the distroless image. This way, we get a very small, very secure final container that is perfect for production.


## 9. What’s the difference between Docker volumes and Bind mounts?  
The main difference between Docker volumes and bind mounts is where the data is stored and how it’s managed.

**Bind mounts** directly connect a folder on your own host machine to a folder inside the container. This allows instant two-way synchronization — if you change a file in your system, it changes inside the container, and vice-versa. Bind mounts are commonly used in development because they allow you to edit code locally and see the changes reflected inside the running container immediately, making development faster and smoother.

**Volumes** are storage spaces that Docker creates and manages by itself, outside of the container but still on the host machine. They allow a container to store data permanently, even if the container stops, restarts, or gets deleted. The main purpose of volumes is to have a safe and consistent way of storing persistent data, especially in production environments, where Docker manages the storage location and performance optimizations behind the scenes.


## 10. Docker Networking: Bridge vs Host vs Overlay  
**Bridge Network**  
Default network created by Docker for containers on a single host. Lets containers communicate with each other securely while isolating them from the host network.  

**Host Network**  
Containers share the host’s network stack. It performs better (since there’s no network translation), but it’s less secure because the container is directly exposed to the outside network. It’s often used for performance-critical apps or when you need full host network access.   

**Overlay Network**  
Overlay networks are used when your Docker containers are not all on the same physical or virtual machine (like Docker Swarm or Kubernetes). Enables secure cross-host communication between containers. Docker creates an encrypted VXLAN overlay network. Containers on different hosts appear as if on the same network.  

**Securing containers with custom bridge network**  
By default, Docker creates a bridge network called bridge. All containers connected to it can communicate freely, and ports are exposed to the host. This can be insecure for production or multi-container setups because containers can see each other even if they don’t need to.

A custom bridge network allows you to:
- Isolate containers that don’t need to talk to each other.
- Control communication between containers explicitly.
- Avoid exposing unnecessary ports to the host.
- Give containers predictable DNS names for easier inter-container communication.

## 11. Docker Compose
Docker Compose is a tool that lets you define and manage multi-container applications using a single YAML file.
Instead of running multiple docker run commands, you describe everything — containers, networks, volumes, environment variables — in a single file (docker-compose.yml) and start them all at once using docker-compose up.
When your app has multiple services (like frontend, backend, and database), manually linking containers becomes messy.
Docker Compose simplifies this — it manages dependencies, networking, and lifecycle (start, stop, rebuild) of all services together.

```
services:  
  accounting:    
    image: ${IMAGE_NAME}:${DEMO_VERSION}-accounting   
    container_name: accounting   
    build:   
      context: ./   
      dockerfile: ${ACCOUNTING_DOCKERFILE}   
      cache_from:   
        - ${IMAGE_NAME}:${IMAGE_VERSION}-accounting   
    deploy:   
      resources:   
        limits:   
          memory: 120M    
    restart: unless-stopped   
    environment:    
      - KAFKA_ADDR   
      - OTEL_EXPORTER_OTLP_ENDPOINT=http://${OTEL_COLLECTOR_HOST}:${OTEL_COLLECTOR_PORT_HTTP}   
      - OTEL_EXPORTER_OTLP_METRICS_TEMPORALITY_PREFERENCE   
      - OTEL_RESOURCE_ATTRIBUTES    
      - OTEL_SERVICE_NAME=accounting     
      - DB_CONNECTION_STRING=Host=${POSTGRES_HOST};Username=otelu;Password=otelp;Database=${POSTGRES_DB}    
      - OTEL_DOTNET_AUTO_TRACES_ENTITYFRAMEWORKCORE_INSTRUMENTATION_ENABLED=false   
    depends_on:    
      otel-collector:   
        condition: service_started   
      kafka:   
        condition: service_healthy   
    logging: *logging   
```


**ports**  
1. Accessing a container from a browser.  
`ports: - "8080:80"`  
- 80 = internal port inside the container  
- 8080 = external port on the EC2 machine or your local computer

2. Container-to-container communication.  
Inside docker-compose, all containers are in the same internal network. They communicate using service name + internal port, NOT the exposed port.  Even if we don’t write ports at all, other containers can still access it because Docker auto-connects containers using service name.  
Note: If we dont want the contaierns to talk, just seperate the network.-

## 12. You're in charge of maintaining Docker environments in your company and You've noticed many stopped containers and unused networks taking up space. Describe how you would clean up these resources effectively to optimize the Docker environment?
The `docker prune` command is used to clean up unused Docker resources, such as containers, volumes, networks, and images. It helps reclaim disk space and tidy up the Docker environment by removing objects that are not in use.

There are different types of `docker prune` commands:

- `docker container prune`: Removes stopped containers.
- `docker volume prune`: Deletes unused volumes.
- `docker network prune`: Cleans up unused networks.
- `docker image prune`: Removes unused images.

Running **`docker system prune`** combines these functionalities into one command, ensuring that Docker removes any resources not associated with a running container.

- *Note: The **docker system prune** command should be used with caution as it permanently deletes unused resources. Any data associated with them will be lost.*


## 13. A company wants to create thousands of Containers. Is there a limit on how many containers you can run in Docker?
There is no fixed hard-coded limit inside Docker for how many containers you can run — Docker doesn’t say “maximum 1000 containers” or anything like that. The real limit comes from your machine’s resources such as CPU, RAM, storage speed, and network capacity, because each container consumes a bit of processing power and memory. So companies can run thousands (or even tens of thousands) of containers, but they don’t do it on a single computer — they use container orchestration systems like Kubernetes or Docker Swarm to distribute those containers across many servers. This works by creating a cluster of machines where containers are spread intelligently based on available resources, allowing large-scale deployment efficiently and without overloading a single host system.


## 14. You're managing a Docker environment and need to ensure that each container operates within defined CPU and memory limits. How do you limit the CPU and memory usage of a Docker container?
Docker allows you to limit the CPU and memory usage of a container using resource constraints. You can set the CPU limit with the --cpu option and the memory limit with the --memory option when running the container using the docker run command.

For example, **`docker run --cpu 2 --memory 1g mycontainer`** limits the container to use a maximum of 2 CPU cores and 1GB of memory.


## 15. What is the purpose of the CMD instruction in a Dockerfile?
The `CMD` instruction in a Dockerfile specifies the default command to be executed when a container is started from the Docker image. It defines the primary functionality of the container, such as running an application or executing a script. If a Docker container is started without specifying a command, the command specified in the `CMD` instruction will be executed by default.


## 16. You're part of a development team deploying a microservices architecture using Docker containers. One of the containers, critical to the system's functionality, has suddenly started failing without clear error messages. How do you debug issues in a failing Docker container?
There are several techniques to debug issues in a Docker container:

**Logging**: Check the container logs using **`docker logs <container>`** because this shows what the application inside the container was outputting when it crashed.

**Shell access**: Run the container in interactive mode or exec into it using **`docker exec-it <container> /bin/sh or /bin/bash`** to directly inspect files, processes, or configurations from inside.

**Image inspection**: You can inspect the Docker image's contents and configuration using **`docker image inspect`**. This lets you check for potential misconfigurations or missing dependencies.

**Health checks**: Docker supports defining health checks for containers, allowing you to monitor the health status and automatically restart or take action based on predefined conditions.


## 17. You're working on a critical application running in Docker containers, and an update needs to be applied without risking data loss. How do you update a Docker container without losing data?
The steps to update a Docker container without losing data are:

- Store the data outside the container itself, using Docker volumes.    
- Stop the container gracefully using the **`docker stop`** command.   
- Pull the latest version of the container image using **`docker pull`.**   
- Start a new container using the updated image, while attaching the same volume so the new container continues using the existing persistent data.   
- Verify that the new container is functioning correctly and that the data is still intact. 


## 18. You're responsible for securing the Docker containers hosting your organization's sensitive applications. How do you secure Docker containers?
The way you do this is by using minimal base images (like Alpine or distroless), running containers as non-root users, only exposing necessary ports, limiting runtime permissions with flags like --read-only and --cap-drop, scanning images for vulnerabilities, and keeping images and dependencies updated. You also secure data access through volumes with correct permissions, restrict environment secrets using tools like Docker secrets or external secret managers, and use network segmentation so containers only communicate with services they actually need.

## 19. How do you monitor Docker containers?
There are various ways to monitor Docker containers, including:

- Using Docker's built-in container monitoring commands, such as **docker stats** and **docker container stats**, to view resource usage statistics.  
- Integrating with container monitoring and logging tools like Prometheus, Grafana, or ELK stack (Elasticsearch, Logstash, Kibana) to collect and analyze container metrics and logs.  
- Leveraging container orchestration platforms that offer built-in monitoring capabilities such as Docker Swarm's service metrics or Kubernetes' metrics API.   
- Using specialized monitoring agents or tools that provide container-level insights and integration with broader monitoring and alerting systems.   


## 20. What are some best practices for using Docker in production environments?
Some best practices for using Docker in production environments include:

- Building and using lightweight container images to improve deployment speed and reduce the attack surface.    
- Regularly updating Docker and the underlying host system with security patches.    
- Implementing container orchestration platforms, such as Docker Swarm or Kubernetes, to manage containers at scale and provide features like load balancing and service discovery.   
- Configuring resource limits for containers to prevent resource contention and ensure fair allocation.   
- Monitoring container health, resource usage, and application metrics for performance optimization and troubleshooting.   
- Implementing secure network configurations such as using private networks and encrypting container traffic.    
- Backing up critical data and using volume or storage solutions that provide data persistence and redundancy.    
- Implementing a comprehensive security strategy, including container vulnerability scanning, access controls, and least privilege principles.   

## How to write Dockerfile?

1. `FROM`: Mention base image here. To decide the right base image, use these signals:   

**a. Runtime requirement:**    
If app needs Node.js and npm, then the base image must provide exactly that runtime. Like `CMD` runs `npm start` and `npm` belongs to Node.js. So base image must already contain Node.js + npm

**b. Image size and purpose:**   
i. Is this image used for development or running?   
ii. Do we need build tools inside this image?   

**c. Compatibility with app:**   
Node version mentioned in `package.json` or README must match with the base image.

2. `WORKDIR /app`:   Creates an app folder inside container where all the code will be copied and build tools are installed.

3. `COPY package*.json ./`    Copies the file that list the depedencies. So mostly it is package.json.
4. `RUN npm install`    Reads the copied package.json and install the depedencies.
5. `COPY . .`    Copy the application code.    
6. `RUN npm run build`    React creates files HTML/CSS/JS and save them into folder `/app/build`. So this folder contains complete website.   
7. `COPY --from=builder /app/build /usr/share/nginx/html`    The final built files are copied from `/app/build` and stored in this Nginx directory to server these files in production.     
6. `EXPOSE 3000`    Port that container listens on.     
7. `CMD ["npm", "start"]`     Command required to run the application (check package.json for this command).


Application Question(s):

1. How would you set up Dev, Staging, and Prod on AWS with low cost but proper isolation and reliability?
2. Describe a CI/CD pipeline you built using GitHub/GitLab for frontend and backend deployment.
3. If a prod deploy fails, how do you roll back quickly using Docker images stored in ECR?
4. How do you tag and version Docker images in ECR, and which tagging strategy do you prefer?
5. How would you configure VPC, Security Groups, IAM, and SSL/TLS for a web app on AWS?
6. Why use Postgres in Docker for Dev/Staging and RDS for Prod? What risks do you handle?
7. Why use Nginx as a reverse proxy when ALB exists? When do you use both together?
8. How do you set up CloudWatch logs, metrics, and alerts to catch issues early?
9. How do you manage secrets securely and test backup and restore in AWS?
10. What are your current CTC, expected CTC, notice period, and do you have laptop + internet?