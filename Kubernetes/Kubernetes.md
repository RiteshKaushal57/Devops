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