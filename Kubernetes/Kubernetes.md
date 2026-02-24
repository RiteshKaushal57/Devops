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
apiVersion: apps/v1
kind: Deployment
metadata:
  name: crashloop-example
  labels:
    app: crashlooplearning
spec:
  replicas: 1
  selector:
    matchLabels:
      app: crashlooplearning
  template:
    metadata:
      labels:
        app: crashlooplearning
    spec:
      containers:
      - name: crashlooplearning
        image: abhishekf5/crashlooptest:v2
        ports:
        - containerPort: 8000
        livenessProbe:
         exec:
          command:
           - cat
           - /tmp/healthy
         initialDelaySeconds: 0
         periodSeconds: 1
```

- periodSeconds: It tells kubelet how frequently it has to check the health of the pod.


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

*Helm Chart = packaged blueprint of your application (like a Docker image).*    
*Helm Release = actual running installation of that chart (like a Docker container).*

## 23. What is Stateless and Stateful applications?
- **Stateless application**  
Stateless applications are those that do not store any client or session data on the server, meaning each request is independent and can be handled by any pod of the application. This makes them easy to scale and recover, as any pod can serve any request. Examples include web servers serving static pages or API servers where session data is stored externally in a database or cache.

- **Stateful application**  
Stateful applications keep data or session information inside the application or pod, giving each pod a unique identity and state that clients may depend on. These are harder to scale and manage because you cannot replace pods randomly without losing data. Examples include databases like MySQL, PostgreSQL, or messaging systems like Kafka.

## 24. How do you manage Stateful applications?
Stateful applications in Kubernetes are managed differently from stateless ones because they store data and require stable identity. You use special Kubernetes resources and design patterns to ensure data safety, stable networking, and controlled scaling.

To manage stateful applications, Kubernetes provides a **StatefulSet**, which is similar to a Deployment but designed for apps that need stable pod names and persistent storage. Each pod in a StatefulSet gets a unique and stable identity like `db-0`, `db-1`, `db-2`, and these names do not change even if the pod restarts. This is important for databases and clustered systems.

Stateful applications also require **Persistent Volumes (PV) and Persistent Volume Claims (PVC)** so that data is not lost when a pod restarts or is rescheduled to another node. Kubernetes ensures that each pod keeps its own dedicated storage.

Additionally, a **Headless Service** is usually used with StatefulSets. Unlike a normal service, it does not load balance traffic. Instead, it gives direct DNS access to each pod, which allows databases and clustered systems to communicate properly with specific nodes.

Scaling is done carefully. When you scale up, new pods are created in order (e.g., `db-3` after `db-2`). When scaling down, pods are terminated in reverse order to protect data consistency. Updates also happen one pod at a time to avoid disrupting the entire cluster.

In summary, stateful applications are managed using:

* StatefulSet (for stable identity and ordered deployment)
* Persistent storage (to retain data)
* Headless Service (for direct pod access)
* Careful scaling and rolling updates

This ensures reliability, data safety, and proper cluster behavior for databases and other stateful systems.


## 25. What is headless service?
A Headless Service in Kubernetes is a special type of service that does not get a cluster IP, so it doesn’t load-balance traffic automatically. Instead, it allows clients to directly access the individual pods that match its selector.

This is useful for stateful applications where each pod has a unique identity, like databases (MySQL, PostgreSQL), Kafka, or Cassandra. With a normal Service, traffic is distributed among pods randomly, but with a Headless Service, DNS resolves the service name to the IP addresses of all pods, allowing clients to connect to specific pods directly.

## Kubernetes Troubleshooting

### 1. ImagePullBackOff
It is error related to pulling the container image onto the Kubernetes cluster. There are two scenerios for this image:  
1. When image name is invalid or image is non-existent.  
2. When the image is private. (Put imagePullSecret: <docker credentials> so that private image is pulled.)

### 2. CrashLoopBackOff
When you see "CrashLoopBackOff," it means that kubelet is trying to run the container, but it keeps failing and crashing. After crashing, Kubernetes tries to restart the container automatically, but if the container keeps failing repeatedly, you end up in a loop of crashes and restarts, thus the term "CrashLoopBackOff."

- **Misconfigurations**    
Misconfigurations can encompass a wide range of issues, from incorrect environment variables to improper setup of service ports or volumes. These misconfigurations can prevent the application from starting correctly, leading to crashes. For example, if an application expects a certain environment variable to connect to a database and that variable is not set or is incorrect, the application might crash as it cannot establish a database connection.

- **Errors in the Liveness Probes**  
Liveness probes in Kubernetes are used to check the health of a container. If a liveness probe is incorrectly configured, it might falsely report that the container is unhealthy, causing Kubernetes to kill and restart the container repeatedly. For example, if the liveness probe checks a URL or port that the application does not expose or checks too soon before the application is ready, the container will be repeatedly terminated and restarted.
```
livenessProbe:
         exec:
          command:
           - cat
           - /tmp/healthy
         initialDelaySeconds: 0
         periodSeconds: 1
```

- **The Memory Limits Are Too Low**    
If the memory limits set for a container are too low, the application might exceed this limit, especially under load, leading to the container being killed by Kubernetes. This can happen repeatedly if the workload does not decrease, causing a cycle of crashing and restarting. Kubernetes uses these limits to ensure that containers do not consume all available resources on a node, which can affect other containers.
```
resources:
         limits:
          cpu: "25m"
          memory: "25Mi"
```
*Note: Don't increase the CPU or memory. It is not the solution and very bad practice. Instead find pod is using so much resources then expected.*

- **Wrong Command Line Arguments**    
Containers might be configured to start with specific command-line arguments. If these arguments are wrong or lead to the application exiting (for example, passing an invalid option to a command), the container will exit immediately. Kubernetes will then attempt to restart it, leading to the CrashLoopBackOff status. An example would be passing a configuration file path that does not exist or is inaccessible.

- **Bugs & Exceptions**    
Bugs in the application code, such as unhandled exceptions or segmentation faults, can cause the application to crash. For instance, if the application tries to access a null pointer or fails to catch and handle an exception correctly, it might terminate unexpectedly. Kubernetes, detecting the crash, will restart the container, but if the bug is triggered each time the application runs, this leads to a repetitive crash loop.

### 3. What is Node Selector?
A **Node Selector** in Kubernetes is a simple way to **tell Kubernetes where a pod should run**. You use it **when** you want a particular application to run only on specific nodes, **why** because not all nodes are the same (some may have more CPU, GPU, SSD, or be in a specific zone), and **how** by labeling nodes and then asking Kubernetes to place pods only on nodes with matching labels. In simple terms, you first add a label to a node (for example, `type=high-memory`), and then in the pod or deployment YAML you specify a node selector with the same label. Kubernetes checks all worker nodes, finds the ones with that label, and schedules the pod only on those nodes. If no node matches, the pod will stay pending. So, Node Selector is a basic scheduling rule that gives you control over *which kind of node* runs your application.
```
spec:
    containers:
    - name: my-app
    image: my-image
    nodeSelector:
    disktype: ssd
```

### 4. What is Node Affinity?
**Node Affinity** is a more advanced and flexible way to control **where pods are scheduled** in a Kubernetes cluster. You use it **when** you want stronger or more expressive placement rules than nodeSelector, **why** because real clusters have many nodes with different capabilities and constraints, and **how** by defining rules that tell Kubernetes which nodes a pod *must* run on or *should preferably* run on. Node Affinity works by matching pod rules with node labels and supports two main types:    **requiredDuringSchedulingIgnoredDuringExecution**, which is a hard rule where the pod will only be scheduled on matching nodes,  **preferredDuringSchedulingIgnoredDuringExecution**, which is a soft rule where Kubernetes tries to place the pod on preferred nodes but can fall back to others if needed. In simple terms, Node Affinity lets you precisely guide the scheduler to place pods on the right nodes using mandatory or preferred conditions, giving much more control than nodeSelector.
```
spec:
    containers:
    - name: my-app
    image: my-image
    affinity:
    nodeAffinity:
        requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
            - key: disktype
            operator: In
            values:
            - ssd
```

### 5. What is Taints and Tolerations?
**Taints and Tolerations** are a Kubernetes mechanism used to **control which pods can run on which nodes**. By default, Kubernetes allows any pod to run on any worker node if resources are available. Taints and tolerations change this behavior by letting **nodes repel pods**, unless a pod explicitly says it can tolerate that node.

- **Taints** are applied to nodes to repel certain pods. They allow nodes to refuse pods unless the pods have a matching toleration.  
- **Tolerations** are applied to pods and allow them to schedule onto nodes with matching taints. They override the effect of taints.

**Example:** Suppose you have a node reserved only for **database workloads**, and you don’t want normal application pods to run on it.

**Step 1. Add a taint to the node**  
```
kubectl taint nodes node-1 dedicated=database:NoSchedule
```
This means:  
* `dedicated=database` → identifies the purpose of the node  
* `NoSchedule` → no pod can be scheduled here unless it tolerates this taint

Now the node is saying: *“Only database pods are allowed.”*

**Step 2. Pod without toleration (will NOT be scheduled)**
```
apiVersion: v1
kind: Pod
metadata:
  name: app-pod
spec:
  containers:
  - name: app
    image: nginx
```
This pod has **no toleration**, so Kubernetes will **not** place it on `node-1`.

**Step 3. Pod with toleration (will be scheduled)**  
```
apiVersion: v1
kind: Pod
metadata:
  name: db-pod
spec:
  tolerations:
  - key: "dedicated"
    operator: "Equal"
    value: "database"
    effect: "NoSchedule"
  containers:
  - name: mysql
    image: mysql
```
This pod **tolerates the taint**, so Kubernetes allows it to run on `node-1`.