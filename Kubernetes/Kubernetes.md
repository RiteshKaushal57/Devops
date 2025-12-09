## 1. What is Orchestration tool?
An orchestration tool is software that automatically manages and controls multiple containers or services so they run smoothly without manual effort. We need this tool because once your application grows, manually starting, stopping, fixing, and scaling containers becomes difficult, slow, and risky. It works by continuously monitoring all containers, restarting the ones that fail, adding more when traffic increases, removing extras when traffic drops, and ensuring everything stays healthy and balanced—basically acting like an intelligent manager for your entire system.

## 2. Why do we need Orchestration tool?
We need an orchestration tool because when an application grows and uses many containers, managing them manually becomes confusing and unreliable, so an orchestrator acts like an automatic manager. It is needed because without it, you would have to start, stop, restart, scale, and balance containers yourself, which becomes impossible as the number increases. It works by watching all containers, automatically restarting the ones that fail, adding more when traffic goes up, removing extras when traffic goes down, and keeping everything healthy so the system runs smoothly without manual effort.

## 3. What is the difference between Docker and Kubernetes?
Docker is a containerization platform — it helps you build, package, and run applications inside containers. Kubernetes is a container orchestration tool — it helps you manage, scale, and automate the deployment of those containers.

## 4. Why Kubernetes is used?
Kubernetes is used because it solves limitations that Docker has. So basically, Docker can run only on a single host, cannot auto-scale or self-heal containers, and lacks enterprise-level features. But Kubernetes solves these limitations by running containers across multiple nodes in a cluster, automatically scaling applications based on demand using Horizontal Pod Autoscaler, self-healing failed containers by restarting or rescheduling them, and providing enterprise-grade features like load balancing, rolling updates, and persistent storage.  

## 5. Explain Kubernetes architecture.  
Kubernetes follows a master–worker architecture. The *control plane* is like the brain of the cluster. It decides what runs where, monitors the health of Pods, schedules them onto nodes, and handles updates or rollbacks.

The worker nodes are the muscles of the cluster. They actually run your application Pods using a container runtime and report their status back to the control plane. Each node has a kubelet agent to communicate with the control plane and a kube-proxy to handle networking so Pods can talk to each other. Together, the control plane and nodes keep your apps running reliably and automatically handle failures, scaling, and traffic.

### Control Plane Components (Master Node):

**1. API Server**  
The API Server is the front door of the entire Kubernetes cluster. Every request — whether from kubectl, dashboards, controllers, or even internal components — passes through it. You never talk to Kubernetes directly; you talk to the API Server, and it talks to everything else. It validates requests, updates the cluster state in etcd, and acts as the communication hub that all components rely on.

**2. etcd**  
etcd is the cluster’s memory. It stores every single piece of information about your Kubernetes system — what Pods exist, which Deployments are running, ConfigMaps, Secrets, node status, and more. If something fails and needs to be restored, Kubernetes reads the desired state from etcd and rebuilds the system. Since etcd is the source of truth, it must be highly available and consistent. 

**3. Controller Manager**  
The Controller Manager is the “watchdog” that constantly monitors the cluster and fixes anything that drifts from the desired state. It watches over components like ReplicaSets, Nodes, Deployments, and more. If a Pod disappears, if a node goes down, or if a replica count doesn’t match what you asked for, the Controller Manager immediately takes action to correct it.

**4. Scheduler**  
The Scheduler decides where each Pod should run. When a Pod is created but not yet assigned to a node, the Scheduler looks at all available nodes, checks factors like CPU, RAM, taints, tolerations, and affinity rules, and selects the best node for running that Pod. It ensures optimal resource usage and balances the workload across the cluster.

### Worker Node Components:

**1. Kubelet**  
Kubelet is the node’s personal manager. It receives instructions from the API Server — such as “run this Pod” or “restart this container” — and ensures they happen. It constantly checks if containers are healthy and reports their status back to the control plane. Its job is simple: whatever is supposed to be running on the node should be running properly.

**2. Kube-Proxy**  
Kube-Proxy is responsible for networking inside the cluster. It ensures Pods can talk to each other, and Services can route traffic to Pods. It creates the necessary forwarding rules, does basic load balancing, and manages how requests move across nodes. Without kube-proxy, pod-to-pod and service-to-pod communication would fail.

**3. Container Runtime**  
The container runtime is the engine that actually runs your containers. Kubernetes itself does not run containers — it delegates the work to a runtime like Docker, containerd, or CRI-O. The runtime pulls container images, starts containers, stops them, and reports their status to the Kubelet.

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

## 10. What is Service?
Kubernetes Service allows Pods to communicate with each other or with the outside world. Since Pods are ephemeral and can restart, their IP addresses can change, which would break direct communication. Services solve this by not relying on IPs; instead, they act as a proxy and load balancer using labels and selectors. Each Pod has a label, and the Service routes traffic to the correct Pods based on these labels. This way, communication works reliably even if Pod IPs change.

There are four main types of Services:

**1. ClusterIP (default):**   
Exposes the service internally within the cluster for Pod-to-Pod communication.

**2. NodePort**:  
 Opens a fixed port on all worker nodes to allow external traffic to reach the Pods.

**3. LoadBalancer:**   
Creates an external cloud load balancer that distributes incoming traffic across Pods, commonly used in production.

**4. ExternalName:**  
 Maps the service to an external DNS name, allowing Pods to access resources outside the cluster.

**How to write service resources in Kubernetes?**   
``` 
It starts with:  
`api version: /v1`
`kind: service`
- metadata:  
`name*`
`labels*`

- spec:  
`type`: Defines the type of service i.e ClusterIp/Nodeport/LB.  
- ports:  
`port`: It is not a container port. It is port for the service so that pod and service can communicate with each other. Ex: nameofservice:portofservice  
`targetport`: port of the container. Pod for which you are creating service.  
`selector`:
```
## 11. What is kubernetes ingress?  
A **Kubernetes Ingress** is a way to manage how services in a cluster can be accessed by the internet. Ingress is used because if you have multiple services, exposing each one separately to the internet can be messy and inefficient. It works by using an **Ingress resource** that defines rules (like URL paths or hostnames) and an **Ingress controller** that reads these rules and routes incoming traffic to the correct service allowing clean, secure, and organized access to your applications from outside the cluster.

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

The reason we use them is to *separate configuration and sensitive data from application code*, making apps more secure, portable, and easier to manage. You create them with YAML files or `kubectl` commands and then attach them to pods via environment variables, volumes, or command arguments.

## 15. What exactly is a service account?
So in general there are two things. One is user account and one is service account.
**User account** is basically for users to connect to Kubernetes cluster. User account will have a kubeconfig file and using kubectl you connect to that Kubernetes cluster.   
**Service account** Let's take a example of pod. So if you want to deploy a pod to Kubernetes and this pod is basically a microservice or it is an application. So for this microservice or application to run on Kubernetes, just like how a user has user account, this service should have a service account. Just like user requires permission to do something on Kubernetes, service also requires permission to do something on Kubernetes and that permission comes through the service account.

In Kubernetes it is not possible to run pod without service account. In case you don't assign your pod a service account, Kubernetes does will assign default service account to your pod. If Kubernetes is creating a default service account, it comes with default permissions like that pod can run on Kubernetes cluster.

But if you want to create permissions, then service account should be assigned with a role or cluster role and and this assigning hppens due to *bindng role*. So basically inside role or cluster role, you define permissions and using a binding role or cluster role binding, you just bind the service account to the role.

## 16. What is deployment resource?
A **Deployment** in Kubernetes is a resource that manages how your application should run and stay healthy. We use a Deployment because running containers directly is risky — if one fails, nothing brings it back, and updating the app manually is messy. A Deployment solves this by automatically creating and managing ReplicaSets, which in turn maintain the correct number of Pods. It also handles rolling updates, rollbacks, and ensures your app always stays available.

**How to write deployment resources in Kubernetes?**    
```
It starts with:  
`api version: app/v1`
`kind: deployment`
- Due to api version and kind, Kubernetes understands what kind of resource it is.  
- metadata:  
`name*`: Name of the deployment.  
`labels*`: Use to identify the microservice. Will be used as selecter.   

- spec:  Differs from section to section. It writes deployment related stuff.  
`replicas*`: Replica set needs this information.  
`template`:- Below this defines pod related configuration.  
`metadata`: Useful for service discovery.
`labels`: Again it is useful for service discovery. 
`spec`:-  
`serviceaccountname`: Name the service or it will choose default one.  
`containers`  
`   -name`: Name of service.   
`   -image`:  
`   -port`:  *written in targetport.   
`env`:  
`volumes`:
```

## 17. How does Kubernetes auto-scale?
Kubernetes **auto-scales** your applications by automatically adjusting the number of Pods based on demand. It works using the **Horizontal Pod Autoscaler (HPA)**, which monitors metrics like CPU, memory, or custom metrics, and increases or decreases the number of Pods to match the workload. This ensures your application stays responsive, efficient, and cost-effective without manual intervention.

## 18. What is the init container?
An init container is a type of container in Kubernetes that runs before the main application containers in a pod. The purpose of an init container is to perform initialization tasks or setup procedures that are not present in the application container images.

Examples of tasks that an init container might perform include downloading configuration files, setting up a network connection, or initializing a database schema.

## 19. Company is very concerned about Securing Clusters. List some security measures that you can take while using Kubernetes.
**1. Enable Role-Based Access Control (RBAC)**  
Control who can access what in the cluster. RBAC ensures developers, admins, and services only get the permissions they actually need — preventing accidental or malicious access.

**2. Use Network Policies**  
Network Policies control which Pods can talk to each other.
This prevents unauthorized internal communication and stops attackers from moving laterally inside the cluster.

**3. Secure the API Server**  
Limit API access using authentication, authorization, and TLS.
Only trusted users and services should be allowed to reach the control plane.

**4. Use Secrets for Sensitive Data**  
Store passwords, tokens, and keys in Kubernetes Secrets instead of hardcoding them in YAML files.
Use encryption at rest so even the stored Secrets are protected.

**5. Image Security (Scan and Verify)**  
Use trusted container images, regularly scan them for vulnerabilities, and enforce signing (e.g., Cosign). This prevents attackers from sneaking malicious code into your supply chain.

**6. Isolate Workloads with Namespaces**  
Namespaces help separate teams or environments (dev, QA, prod).
Applying separate policies reduces blast radius in case of compromise.

**7. Restrict Access to etcd**  
etcd stores cluster secrets and configuration — treat it like a database full of passwords.
Enable TLS and restrict access to control-plane-only.

**8. Use Logging and Monitoring Tools**  
Use Prometheus, Grafana, ELK/EFK, or Datadog to detect unusual Pod behavior.
Helps identify threats like resource abuse or compromised containers.

## 20. You need to ensure that a specific pod remains operational at all times. How to make sure that pod is always running?
We can use liveness probes. A liveness probe always checks if an application in a pod is running, if this check fails the container gets restarted. This is ideal in many scenarios where the container is running but somehow the application inside a container crashes.
```
apiVersion: v1
kind: Pod
metadata:
  name: my-pod
spec:
  containers:
  - name: liveness
    image: k8s.gcr.io/liveness
    args: 
    - /server
    livenessProbe:
      httpGet:
        path: /healthz
```

## 21. What is Helm?
Helm is a package manager for  Kubernetes the allows you to install Kubernetes contollers (Prometheus, Grafana) or third party applications, update their version, uninstall these too. Helm also allows us to bundle or package the application as Helm charts so that any people outside your organization can use the helm command to install your application.  
1st step: Add the repository (chart).  
2nd step: Run the helm install command.  

## 22. Components of Helm
- **1. Helm Chart:**  
A Helm Chart is a package that contains all the Kubernetes manifests needed to deploy an application. Instead of writing multiple YAML files for pods, services, deployments, config maps, etc., a Helm Chart groups them together and makes them reusable.

### Components of a Chart:
**1. Chart.yaml**   
This file contains metadata about the chart — basically the identity card of the chart.
It includes things like the chart name, version, description, and the app version.

**2. values.yaml**    
This file contains the default configuration values for the chart.
Whatever you want to make configurable (image name, port, replicas, resource limits, etc.) goes here.
Users can override these values during installation to customize the deployment.

**3. templates/ folder**    
This folder contains all the Kubernetes YAML templates (Deployment, Service, Ingress, ConfigMaps, etc.).
These templates read the values from values.yaml and generate the final manifest that gets applied to the cluster.
Basically, these are the actual “blueprints” for Kubernetes objects.

**4. charts/ folder**     
This folder contains dependency charts.
If your application depends on other charts (like a database, cache, or another service), those packaged charts are stored here.
It’s not used in simple applications but becomes important for large systems.

- **2. Helm Repositories:**  
A Helm repository is a place where Helm charts are stored and shared. It’s similar to a package repository in Linux (like APT or YUM repos).

- **3. Helm release**  
A Helm Release is simply an installed and running instance of a Helm Chart inside your Kubernetes cluster.  
*Helm Chart = packaged blueprint of your application (like a Docker image). Helm Release = actual running installation of that chart (like a Docker container).*

## 23. What is Stateless and Stateful applications?
- **Stateless application**  
Stateless applications are those that do not store any client or session data on the server, meaning each request is independent and can be handled by any pod of the application. This makes them easy to scale and recover, as any pod can serve any request. Examples include web servers serving static pages or API servers where session data is stored externally in a database or cache.

- **Stateful application**  
Stateful applications keep data or session information inside the application or pod, giving each pod a unique identity and state that clients may depend on. These are harder to scale and manage because you cannot replace pods randomly without losing data. Examples include databases like MySQL, PostgreSQL, or messaging systems like Kafka.