## What is the difference between Docker and Kubernetes?
Docker is a containerization platform — it helps you build, package, and run applications inside containers. Kubernetes is a container orchestration tool — it helps you manage, scale, and automate the deployment of those containers.  

## Why Kubernetes is used?
Kubernetes is used because it solves limitations that Docker has. So basically, Docker can run only on a single host, cannot auto-scale or self-heal containers, and lacks enterprise-level features. But Kubernetes solves these limitations by running containers across multiple nodes in a cluster, automatically scaling applications based on demand using Horizontal Pod Autoscaler, self-healing failed containers by restarting or rescheduling them, and providing enterprise-grade features like load balancing, rolling updates, and persistent storage.  

## Explain Kubernetes architecture.  
Kubernetes follows a master–worker architecture. The control plane (master) manages the cluster and includes components like API Server, etcd, Controller Manager, and Scheduler. The worker nodes actually run the containers and include Kubelet, Kube-Proxy, and a container runtime. This separation helps Kubernetes manage, schedule, and scale containers automatically while keeping the system reliable and self-healing.

### Control Plane Components (Master Node):

**1. API Server**
- Acts as the gateway for all communication.  
- You (or kubectl) talk to the API Server.  
*Example: kubectl get pods → goes to API Server.*  

**2. etcd**
- A key-value database that stores all cluster data (state, config, secrets, etc.).  
*Example: if a pod crashes, etcd helps restore its desired state.*

**3. Controller Manager**
- Ensures the cluster is always in the desired state.  
*Example: if a pod dies, Controller Manager creates a new one.*

**4. Scheduler**
- Decides which node should run a new pod.  
- Checks resource availability (CPU, RAM, etc.).

### Worker Node Components:

**1. Kubelet**
- The agent on each node that talks to the Control Plane.  
- Ensures the pods are running as instructed.

**2. Kube-Proxy**
- Handles networking and load balancing inside the cluster.  
- Ensures communication between pods and services.

**3. Container Runtime**
- Actually runs containers (e.g., Docker, containerd, CRI-O).

## What is Kubernetes Production System?
A Kubernetes production system is a multi-node, highly available cluster used to run real-world applications at scale. It includes a control plane, worker nodes, networking, storage, monitoring, and CI/CD integration.  

## What are Pods?
A Pod is the smallest and simplest deployable unit in Kubernetes.
Think of it as a wrapper around one or more containers that share the same network, storage, and configuration.  

## What is the difference between a container, pod and a deployment?
A container is the actual app running in isolation. A pod is a wrapper that holds one or more containers and provides them with shared networking, storage, and lifecycle management. A deployment is a higher-level Kubernetes object that manages pods declaratively — it ensures the desired number of pods are running, handles scaling, rolling updates, and automatically replaces failed pods. So, containers run the app, pods package the containers for management, and deployments automate pod management at scale.

## What is namespace in Kubernetes?
Namespaces in Kubernetes are logical partitions within a cluster. They allow you to organize and isolate resources like pods, services, and deployments. Namespaces also help manage access and set resource limits, making it easier to control and manage multiple teams or projects in the same cluster.

## What are different types of services in Kubernetes?
Kubernetes has four main types of services:
**1. ClusterIP** – The default type, used for communication inside the cluster.
**2. NodePort** – Exposes the service on a static port on each node for external access.
**3. LoadBalancer** – Provides an external IP and load balancing, usually in cloud environments.
**4. ExternalName** – Maps the service to an external DNS name for accessing external resources.  

## What is kubernetes ingress?
In Kubernetes, Ingress is used to manage external access to applications inside the cluster. Instead of exposing each app separately with NodePort or LoadBalancer, Ingress provides a single entry point and routes traffic based on the URL or domain name. It also supports load balancing and HTTPS. In short, Ingress acts like a smart traffic manager that decides how incoming requests reach the right Service inside Kubernetes.