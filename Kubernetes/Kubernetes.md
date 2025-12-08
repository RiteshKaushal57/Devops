## 1. What is Orchestration tool?
An orchestration tool is software that automatically manages and controls multiple containers or services so they run smoothly without manual effort. We need this tool because once your application grows, manually starting, stopping, fixing, and scaling containers becomes difficult, slow, and risky. It works by continuously monitoring all containers, restarting the ones that fail, adding more when traffic increases, removing extras when traffic drops, and ensuring everything stays healthy and balanced—basically acting like an intelligent manager for your entire system.

## 2. Why do we need Orchestration tool?
We need an orchestration tool because when an application grows and uses many containers, managing them manually becomes confusing and unreliable, so an orchestrator acts like an automatic manager. It is needed because without it, you would have to start, stop, restart, scale, and balance containers yourself, which becomes impossible as the number increases. It works by watching all containers, automatically restarting the ones that fail, adding more when traffic goes up, removing extras when traffic goes down, and keeping everything healthy so the system runs smoothly without manual effort.

## 3. What is the difference between Docker and Kubernetes?
Docker is a containerization platform — it helps you build, package, and run applications inside containers. Kubernetes is a container orchestration tool — it helps you manage, scale, and automate the deployment of those containers.

## 4. Why Kubernetes is used?
Kubernetes is used because it solves limitations that Docker has. So basically, Docker can run only on a single host, cannot auto-scale or self-heal containers, and lacks enterprise-level features. But Kubernetes solves these limitations by running containers across multiple nodes in a cluster, automatically scaling applications based on demand using Horizontal Pod Autoscaler, self-healing failed containers by restarting or rescheduling them, and providing enterprise-grade features like load balancing, rolling updates, and persistent storage.  

## 5. Explain Kubernetes architecture.  
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

## 6. What is Kubernetes Production System?
A Kubernetes Production System means a fully set up and managed Kubernetes environment that’s ready to run real business applications — not just for testing or learning. It’s a complete system designed for scalability, reliability, security, and automation.

In production, Kubernetes is usually deployed on cloud platforms like AWS, GCP, or Azure using managed services such as EKS, GKE, or AKS. These setups include multiple nodes, monitoring tools, auto-scaling policies, CI/CD pipelines, and proper networking and storage configurations.
 
## 7. What are Pods?
A Pod is the smallest deployable unit in Kubernetes. It acts as a wrapper around one or more containers that share the same resources like storage, network, and configuration. Kubernetes creates, schedules, and monitors these Pods, and if one fails, it can automatically replace it to keep your app running smoothly.

## 8. What is the difference between a container, pod and a deployment?
A **container** is the smallest piece that actually runs your application code — it includes everything your app needs like libraries and dependencies. But Kubernetes doesn’t manage containers. Instead, Kubernetes groups one or more containers inside a **Pod**. A Pod act as a wrapper around one or more containers that share the same resources like storage, network, and configuration making easier for Kubernetes to control them as one unit.

Now, a **Deployment** is a higher-level object that manages Pods. It helps you control how many Pods should run, ensures they’re always available, and automatically replaces them if something fails or when you update your app.

In simple terms:
- A **container** runs the app.
- A **pod** holds one or more containers.
- A **deployment** manages multiple pods and ensures your app stays healthy and scalable.


## 9. What is namespace in Kubernetes?
A **namespace** in Kubernetes is a way to divide a single cluster into multiple virtual spaces so that different teams or projects can work without interfering with each other.
The main reason we use namespaces is to provide isolation — meaning resources in one namespace won’t clash or affect resources in another. This is especially useful when multiple environments like development, testing, and production share the same cluster.

## 10. What are different types of services in Kubernetes?
In Kubernetes, **services** are used to expose pods and allow communication between different parts of an application or even with the outside world. There are mainly **four types** of services.  

**1. ClusterIP**  
It is the default type. It allows communication between pods inside the cluster only and doesn’t expose anything outside. It’s mostly used for internal communication.

**2. NodePort**   
NodePort makes your application accessible outside the cluster by opening a specific port on each worker node. You can access your app using the node’s IP and that port number.

**3. LoadBalancer**   
LoadBalancer is commonly used in cloud environments. It automatically creates an external load balancer to distribute incoming traffic across multiple pods for better performance and reliability.

**4. ExternalName**   
ExternalName connects a Kubernetes service to an external resource outside the cluster, like an external database or API, using a DNS name instead of an IP.  

## 11. What is kubernetes ingress?  
In Kubernetes, Ingress acts like the main entry point for all the applications running inside the cluster. Normally, each service in Kubernetes has its own IP and port, which makes it hard to access applications from outside. Ingress solves this by providing a single gateway that manages external access to different services. It uses routing rules to decide where to send the traffic — for example, it can route users to different apps based on the URL or domain name.

## 12. Kubernetes RBAC
RBAC means Role-Based Access Control. It’s used to manage who can access the cluster and what actions they can perform.  
RBAC works through four main components:  
**1. Role** — defines what actions are allowed.  
**2. RoleBinding** — assigns that Role to a specific user or group.  
**3. ClusterRole** — same as Role, but applies to the entire cluster, not just one namespace.  
**4. ClusterRoleBinding** — connects a ClusterRole to a user or group across the cluster.

## 13. What are Custom Resources and Custom Controller?
**Custom Resources** in Kubernetes are user-defined objects that are used to add new types of resources and automate custom behaviors. They are created using something called a **CustomResourceDefinition (CRD)**, which tells Kubernetes about the new resource type. Once defined, these custom resources can be managed just like normal Kubernetes objects.    
**Custom Controller** is the logic that watches these resources and performs actions automatically to maintain the desired state.

## 14. Kubernetes CONFIGMAPS & SECRETS
In Kubernetes, **ConfigMaps** and **Secrets** are used to manage configuration and sensitive data for your applications without hardcoding them inside containers.

**ConfigMaps** store non-sensitive configuration data, like environment variables, configuration files, or command-line arguments. They allow you to change your app’s behavior without rebuilding the container.

**Secrets**, on the other hand, are designed to hold sensitive information such as passwords, API keys, or certificates. Kubernetes stores them in a way that’s more secure than plain text, and they can be injected into pods when needed.

The reason we use them is to **separate configuration and sensitive data from application code**, making apps more secure, portable, and easier to manage. You create them with YAML files or `kubectl` commands and then attach them to pods via environment variables, volumes, or command arguments. In short, ConfigMaps and Secrets let Kubernetes handle app configuration and secrets safely and flexibly, keeping your containers clean and secure.

## 15. What exactly is a service account?
So in general there are two things. One is user account and one is service account.
**User account** is basically for users to connect to Kubernetes cluster. User account will have a kubeconfig file and using kubectl you connect to that Kubernetes cluster.   
**Service account** Let's take a example of pod. So if you want to deploy a pod to Kubernetes and this pod is basically a microservice or it is an application. So for this microservice or application to run on Kubernetes, just like how a user has user account, this service should have a service account. Just like user requires permission to do something on Kubernetes, service also requires permission to do something on Kubernetes and that permission comes through the service account.

In Kubernetes it is not possible to run pod without service account. In case you don't assign your pod a service account, Kubernetes does will assign default service account to your pod. If Kubernetes is creating a default service account, it comes with default permissions like that pod can run on Kubernetes cluster.

But if you want to create permissions, then service account should be assigned with a role or cluster role and  and this assigning hppens due to *bindng role*. So basically inside role or cluster role, you define permissions and using a binding role or cluster role binding, you just bind the service account to the role.

## 16. What is deployment resource?
*Scaling and healing* is provided by deployment resource.

So pod handles containers. So Kubernetes use a deployment resource called **deployment** to handle pod. So when deploying a service as a deployment, an intermediate resource called **replica set** is created by deployment resource and this replica set spin up the containers or the pods in Kubrenetes world in this deployment YAML file.

If you say replica count as three, then this replica set is going to make sure that all the time the pod count is three. That means for some reason, if this pod goes down, replica set will immediately create a new pod. So it will always make sure that the count is as mentioned in the deployment. Thus, it is solving the problem of auto healing.

## 17. What is service?
It solves the problem of service discovery. 
Lets assume two contiainers are connected and one of them gets down. You restarted it again but now this time, the IP address of conatiner will change and due to that the two containers wont be connected again because another container will look for old IP address of the downed container. So this problem is called service discovery problem and Kubernetes address this problem using **services**. 

So, basically you create a deployment, which will create replica set and which in turns creates a service. So, all these resources are created by deployment file only.

So, Kubernetes has service resource where two pods won't communicate directly, instead they talk to proxy or a simple load balancer. So, this proxy is a service with which pods communicate. This service does not operate with the IP addresses at all. It operates with a concept called as **labels** and **selectors**. So a service looks at a particular label on the pod. So it doesn't matter if the IP address is changed. So this label has to be unique across your different services of organization. So that when any pod goes down it might come up, come up with a new IP address, but still the label will be the same.