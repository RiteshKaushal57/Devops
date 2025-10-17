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
A Kubernetes Production System means a fully set up and managed Kubernetes environment that’s ready to run real business applications — not just for testing or learning. It’s a complete system designed for scalability, reliability, security, and automation.

In production, Kubernetes is usually deployed on cloud platforms like AWS, GCP, or Azure using managed services such as EKS, GKE, or AKS. These setups include multiple nodes, monitoring tools, auto-scaling policies, CI/CD pipelines, and proper networking and storage configurations. In short, a production system ensures your applications run smoothly, recover automatically if something fails, and scale up or down based on demand — all while staying secure and easy to manage.
 
## What are Pods?
A Pod is the smallest and simplest unit in Kubernetes that runs and manages one or more containers together as a single application environment. It acts as a wrapper around one or more containers that share the same resources like storage, network, and configuration. Kubernetes creates, schedules, and monitors these Pods, and if one fails, it can automatically replace it to keep your app running smoothly.

## What is the difference between a container, pod and a deployment?
A **container** is the smallest piece that actually runs your application code — it includes everything your app needs like libraries and dependencies. But Kubernetes doesn’t manage containers. Instead, Kubernetes groups one or more containers inside a **Pod**. A Pod act as a wrapper around one or more containers that share the same resources like storage, network, and configuration making easier for Kubernetes to control them as one unit.

Now, a **Deployment** is a higher-level object that manages Pods. It helps you control how many Pods should run, ensures they’re always available, and automatically replaces them if something fails or when you update your app.

In simple terms:
- A **container** runs the app.
- A **pod** holds one or more containers.
- A **deployment** manages multiple pods and ensures your app stays healthy and scalable.


## What is namespace in Kubernetes?
A **namespace** in Kubernetes is a way to divide a single cluster into multiple virtual spaces so that different teams or projects can work without interfering with each other.
The main reason we use namespaces is to provide isolation — meaning resources in one namespace won’t clash or affect resources in another. This is especially useful when multiple environments like development, testing, and production share the same cluster.

## What are different types of services in Kubernetes?
In Kubernetes, **services** are used to expose pods and allow communication between different parts of an application or even with the outside world. There are mainly **four types** of services.
**1. ClusterIP**  
It is the default type. It allows communication between pods inside the cluster only and doesn’t expose anything outside. It’s mostly used for internal communication.

**2. NodePort**   
NodePort makes your application accessible outside the cluster by opening a specific port on each worker node. You can access your app using the node’s IP and that port number.

**3. LoadBalancer**   
LoadBalancer is commonly used in cloud environments. It automatically creates an external load balancer to distribute incoming traffic across multiple pods for better performance and reliability.

**4. ExternalName**   
ExternalName connects a Kubernetes service to an external resource outside the cluster, like an external database or API, using a DNS name instead of an IP.  

## What is kubernetes ingress?  
In Kubernetes, Ingress acts like the main entry point for all the applications running inside the cluster. Normally, each service in Kubernetes has its own IP and port, which makes it hard to access applications from outside. Ingress solves this by providing a single gateway that manages external access to different services. It uses routing rules to decide where to send the traffic — for example, it can route users to different apps based on the URL or domain name.

## Kubernetes RBAC
RBAC means Role-Based Access Control. It’s used to manage who can access the cluster and what actions they can perform.  
RBAC works through four main components:  
**1. Role** — defines what actions are allowed.  
**2. RoleBinding** — assigns that Role to a specific user or group.  
**3. ClusterRole** — same as Role, but applies to the entire cluster, not just one namespace.  
**4. ClusterRoleBinding** — connects a ClusterRole to a user or group across the cluster.

## What are Custom Resources and Custom Controller?
**Custom Resources** in Kubernetes are user-defined objects that are used to add new types of resources and automate custom behaviors. They are created using something called a **CustomResourceDefinition (CRD)**, which tells Kubernetes about the new resource type. Once defined, these custom resources can be managed just like normal Kubernetes objects.  
A **Custom Controller** is the logic that watches these resources and performs actions automatically to maintain the desired state.

## Kubernetes CONFIGMAPS & SECRETS
In Kubernetes, **ConfigMaps** and **Secrets** are used to manage configuration and sensitive data for your applications without hardcoding them inside containers.

**ConfigMaps** store non-sensitive configuration data, like environment variables, configuration files, or command-line arguments. They allow you to change your app’s behavior without rebuilding the container.

**Secrets**, on the other hand, are designed to hold sensitive information such as passwords, API keys, or certificates. Kubernetes stores them in a way that’s more secure than plain text, and they can be injected into pods when needed.

The reason we use them is to **separate configuration and sensitive data from application code**, making apps more secure, portable, and easier to manage. You create them with YAML files or `kubectl` commands and then attach them to pods via environment variables, volumes, or command arguments. In short, ConfigMaps and Secrets let Kubernetes handle app configuration and secrets safely and flexibly, keeping your containers clean and secure.


## EKS Project
Create a cluster using eksctl
```
eksctl create cluster --name demo-cluster --region us-east-1 --fargate
```
whenever you create kubernetes
pods you can attach or you can integrate that IAM roles with your kubernetes
service accounts so that you can talk to any other AWS services