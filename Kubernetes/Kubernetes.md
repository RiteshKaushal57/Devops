### 1. What is Orchestration tool?
An orchestration tool is software that automatically manages and controls multiple containers or services so they run smoothly without manual effort. We need this tool because once your application grows, manually starting, stopping, fixing, and scaling containers becomes difficult, slow, and risky. It works by continuously monitoring all containers, restarting the ones that fail, adding more when traffic increases, removing extras when traffic drops, and ensuring everything stays healthy and balanced—basically acting like an intelligent manager for your entire system.

### 2. Why do we need Orchestration tool?
We need an orchestration tool because when an application grows and uses many containers, managing them manually becomes confusing and unreliable, so an orchestrator acts like an automatic manager. It is needed because without it, you would have to start, stop, restart, scale, and balance containers yourself, which becomes impossible as the number increases. It works by watching all containers, automatically restarting the ones that fail, adding more when traffic goes up, removing extras when traffic goes down, and keeping everything healthy so the system runs smoothly without manual effort.

### 3. What is the difference between Docker and Kubernetes?
Docker is a containerization platform — it helps you build, package, and run applications inside containers. Kubernetes is a container orchestration tool — it helps you manage, scale, and automate the deployment of those containers.

### 4. Why Kubernetes is used?
Kubernetes is used because it solves limitations that Docker has. So basically, Docker can run only on a single host, cannot auto-scale or self-heal containers, and lacks enterprise-level features. But Kubernetes solves these limitations by running containers across multiple nodes in a cluster, automatically scaling applications based on demand using Horizontal Pod Autoscaler, self-healing failed containers by restarting or rescheduling them, and providing enterprise-grade features like load balancing, rolling updates, and persistent storage.  

### 5. Explain Kubernetes architecture.  
Kubernetes follows a master–worker architecture. The *control plane* is like the brain of the cluster. It decides what runs where, monitors the health of Pods, schedules them onto nodes, and handles updates or rollbacks.

The worker nodes are the muscles of the cluster. They actually run your application Pods using a container runtime and report their status back to the control plane. Each node has a kubelet agent to communicate with the control plane and a kube-proxy to handle networking so Pods can talk to each other. Together, the control plane and nodes keep your apps running reliably and automatically handle failures, scaling, and traffic.

#### Control Plane Components (Master Node):

**1. API Server**  
The API Server is the front door of the entire Kubernetes cluster. Every request — whether from kubectl, dashboards, controllers, or even internal components — passes through it. You never talk to Kubernetes directly; you talk to the API Server, and it talks to everything else. It validates requests, updates the cluster state in etcd, and acts as the communication hub that all components rely on.

**2. etcd**  
etcd is the cluster’s memory. It stores every single piece of information about your Kubernetes system — what Pods exist, which Deployments are running, ConfigMaps, Secrets, node status, and more. If something fails and needs to be restored, Kubernetes reads the desired state from etcd and rebuilds the system. Since etcd is the source of truth, it must be highly available and consistent. 

**3. Controller Manager**  
The Controller Manager is the “watchdog” that constantly monitors the cluster and fixes anything that drifts from the desired state. It watches over components like ReplicaSets, Nodes, Deployments, and more. If a Pod disappears, if a node goes down, or if a replica count doesn’t match what you asked for, the Controller Manager immediately takes action to correct it.

**4. Scheduler**  
The Scheduler decides where each Pod should run. When a Pod is created but not yet assigned to a node, the Scheduler looks at all available nodes, checks factors like CPU, RAM, taints, tolerations, and affinity rules, and selects the best node for running that Pod. It ensures optimal resource usage and balances the workload across the cluster.

#### Worker Node Components:

**1. Kubelet**  
Kubelet is the node’s personal manager. It receives instructions from the API Server — such as “run this Pod” or “restart this container” — and ensures they happen. It constantly checks if containers are healthy and reports their status back to the control plane. Its job is simple: whatever is supposed to be running on the node should be running properly.

**2. Kube-Proxy**  
Kube-Proxy is responsible for networking inside the cluster. It ensures Pods can talk to each other, and Services can route traffic to Pods. It creates the necessary forwarding rules, does basic load balancing, and manages how requests move across nodes. Without kube-proxy, pod-to-pod and service-to-pod communication would fail.

**3. Container Runtime**  
The container runtime is the engine that actually runs your containers. Kubernetes itself does not run containers — it delegates the work to a runtime like Docker, containerd, or CRI-O. The runtime pulls container images, starts containers, stops them, and reports their status to the Kubelet.

### 6. What is Kubernetes Production System?
A Kubernetes Production System means a fully set up and managed Kubernetes environment that’s ready to run real business applications — not just for testing or learning. It’s a complete system designed for scalability, reliability, security, and automation.

In production, Kubernetes is usually deployed on cloud platforms like AWS, GCP, or Azure using managed services such as EKS, GKE, or AKS. These setups include multiple nodes, monitoring tools, auto-scaling policies, CI/CD pipelines, and proper networking and storage configurations.
 
### 7. What are Pods?
A Pod is the smallest deployable unit in Kubernetes. It acts as a wrapper around one or more containers that share the same resources like storage, network, and configuration. Kubernetes creates, schedules, and monitors these Pods, and if one fails, it can automatically replace it to keep your app running smoothly.

### 8. What is the difference between a container, pod and a deployment?
A **container** is the smallest piece that actually runs your application code — it includes everything your app needs like libraries and dependencies. But Kubernetes doesn’t manage containers. Instead, Kubernetes groups one or more containers inside a **Pod**. A Pod act as a wrapper around one or more containers that share the same resources like storage, network, and configuration making easier for Kubernetes to control them as one unit.

Now, a **Deployment** is a higher-level object that manages Pods. It helps you control how many Pods should run, ensures they’re always available, and automatically replaces them if something fails or when you update your app.

In simple terms:
- A **container** runs the app.
- A **pod** holds one or more containers.
- A **deployment** manages multiple pods and ensures your app stays healthy and scalable.


### 9. What is namespace in Kubernetes?
A **namespace** in Kubernetes is a way to divide a single cluster into multiple virtual spaces so that different teams or projects can work without interfering with each other.
The main reason we use namespaces is to provide isolation — meaning resources in one namespace won’t clash or affect resources in another. This is especially useful when multiple environments like development, testing, and production share the same cluster.

### 10. What is Service?
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
### 11. What is kubernetes ingress?  
A **Kubernetes Ingress** is a way to manage how services in a cluster can be accessed by the internet. Ingress is used because if you have multiple services, exposing each one separately to the internet can be messy and inefficient. It works by using an **Ingress resource** that defines rules (like URL paths or hostnames) and an **Ingress controller** that reads these rules and routes incoming traffic to the correct service allowing clean, secure, and organized access to your applications from outside the cluster.

### 12. Kubernetes RBAC
RBAC means Role-Based Access Control. It’s used to manage who can access the cluster and what actions they can perform.  
RBAC works through four main components:  
**1. Role** — defines what actions are allowed.  
**2. RoleBinding** — assigns that Role to a specific user or group.  
**3. ClusterRole** — same as Role, but applies to the entire cluster, not just one namespace.  
**4. ClusterRoleBinding** — connects a ClusterRole to a user or group across the cluster.

### 13. What are Custom Resources and Custom Controller?
**Custom Resources** in Kubernetes are user-defined objects that are used to add new types of resources and automate custom behaviors. They are created using something called a **CustomResourceDefinition (CRD)**, which tells Kubernetes about the new resource type. Once defined, these custom resources can be managed just like normal Kubernetes objects.    
**Custom Controller** is the logic that watches these resources and performs actions automatically to maintain the desired state.

### 14. Kubernetes CONFIGMAPS & SECRETS
In Kubernetes, **ConfigMaps** and **Secrets** are used to manage configuration and sensitive data for your applications without hardcoding them inside containers.

**ConfigMaps** store non-sensitive configuration data, like environment variables, configuration files, or command-line arguments. They allow you to change your app’s behavior without rebuilding the container.

**Secrets**, on the other hand, are designed to hold sensitive information such as passwords, API keys, or certificates. Kubernetes stores them in a way that’s more secure than plain text, and they can be injected into pods when needed.

The reason we use them is to *separate configuration and sensitive data from application code*, making apps more secure, portable, and easier to manage. You create them with YAML files or `kubectl` commands and then attach them to pods via environment variables, volumes, or command arguments.

### 15. What exactly is a service account?
So in general there are two things. One is user account and one is service account.
**User account** is basically for users to connect to Kubernetes cluster. User account will have a kubeconfig file and using kubectl you connect to that Kubernetes cluster.   
**Service account** Let's take a example of pod. So if you want to deploy a pod to Kubernetes and this pod is basically a microservice or it is an application. So for this microservice or application to run on Kubernetes, just like how a user has user account, this service should have a service account. Just like user requires permission to do something on Kubernetes, service also requires permission to do something on Kubernetes and that permission comes through the service account.

In Kubernetes it is not possible to run pod without service account. In case you don't assign your pod a service account, Kubernetes does will assign default service account to your pod. If Kubernetes is creating a default service account, it comes with default permissions like that pod can run on Kubernetes cluster.

But if you want to create permissions, then service account should be assigned with a role or cluster role and and this assigning hppens due to *bindng role*. So basically inside role or cluster role, you define permissions and using a binding role or cluster role binding, you just bind the service account to the role.

### 16. What is deployment resource?
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

### 17. How does Kubernetes auto-scale?
Kubernetes **auto-scales** your applications by automatically adjusting the number of Pods based on demand. It works using the **Horizontal Pod Autoscaler (HPA)**, which monitors metrics like CPU, memory, or custom metrics, and increases or decreases the number of Pods to match the workload. This ensures your application stays responsive, efficient, and cost-effective without manual intervention.

### 18. What is the init container?
An init container is a type of container in Kubernetes that runs before the main application containers in a pod. The purpose of an init container is to perform initialization tasks or setup procedures that are not present in the application container images.

Examples of tasks that an init container might perform include downloading configuration files, setting up a network connection, or initializing a database schema.

### 19. Company is very concerned about Securing Clusters. List some security measures that you can take while using Kubernetes.
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

### 20. You need to ensure that a specific pod remains operational at all times. How to make sure that pod is always running?
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


### 21. What is Helm?
Helm is a package manager for  Kubernetes the allows you to install Kubernetes contollers (Prometheus, Grafana) or third party applications, update their version, uninstall these too. Helm also allows us to bundle or package the application as Helm charts so that any people outside your organization can use the helm command to install your application.  
1st step: Add the repository (chart).  
2nd step: Run the helm install command.  

### 22. Components of Helm
- **1. Helm Chart:**  
A Helm Chart is a package that contains all the Kubernetes manifests needed to deploy an application. Instead of writing multiple YAML files for pods, services, deployments, config maps, etc., a Helm Chart groups them together and makes them reusable.

#### Components of a Chart:
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

### 23. What is Stateless and Stateful applications?
- **Stateless application**  
Stateless applications are those that do not store any client or session data on the server, meaning each request is independent and can be handled by any pod of the application. This makes them easy to scale and recover, as any pod can serve any request. Examples include web servers serving static pages or API servers where session data is stored externally in a database or cache.

- **Stateful application**  
Stateful applications keep data or session information inside the application or pod, giving each pod a unique identity and state that clients may depend on. These are harder to scale and manage because you cannot replace pods randomly without losing data. Examples include databases like MySQL, PostgreSQL, or messaging systems like Kafka.

### 24. How do you manage Stateful applications?
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

## 25. What is  Persistent Volumes (PV) and Persistent Volume Claims (PVC)?
A **Persistent Volume (PV)** is a piece of storage in the cluster that has been provisioned either manually by an administrator or dynamically by a storage class. It represents actual storage from systems like AWS EBS, Azure Disk, NFS, or local storage. A PV exists independently of pods, meaning it does not get deleted when a pod is deleted.

**Persistent Volume Claim (PVC)** is a request for storage made by a pod. Instead of directly using a disk, the pod requests storage by specifying requirements like size and access mode. Kubernetes then matches the PVC with an available Persistent Volume and binds them together.

### 25. What is headless service?
A Headless Service in Kubernetes is a special type of service that does not get a cluster IP, so it doesn’t load-balance traffic automatically. Instead, it allows clients to directly access the individual pods that match its selector.

This is useful for stateful applications where each pod has a unique identity, like databases (MySQL, PostgreSQL), Kafka, or Cassandra. With a normal Service, traffic is distributed among pods randomly, but with a Headless Service, DNS resolves the service name to the IP addresses of all pods, allowing clients to connect to specific pods directly.


### 26. What is Liveness Probe and Readiness Probe?
A **Liveness Probe** is used to determine whether a container is still running properly. If the liveness probe fails, Kubernetes assumes the application is stuck or unhealthy and automatically restarts the container to recover it. This helps in self-healing when an application becomes unresponsive or enters a deadlock state.

A **Readiness Probe**, on the other hand, checks whether the application is ready to receive traffic. If the readiness probe fails, Kubernetes temporarily removes the pod from the service endpoints, meaning no traffic is sent to it until it becomes ready again. Unlike liveness probes, readiness probes do not restart the container.

## 27. A Pod is stuck in Pending state — how do you troubleshoot it?
1. First, I would **describe the pod using `kubectl describe pod <pod_name>`**, to check events and see why it is stuck in Pending, because Kubernetes usually shows scheduling issues clearly.

2. Then, I would **check node availability and resources using `kubectl get nodes` and `kubectl describe nodes`**, to verify if there are enough CPU/memory resources to schedule the pod.

3. Next, I would **look for resource requests/limits issues in the pod spec using `kubectl describe pod <pod_name>`**, because high resource requests can prevent scheduling.

4. After that, I would **check for node selectors, taints, and tolerations using `kubectl describe pod <pod_name>`**, since mismatched labels or taints can block scheduling.

5. I would also **verify if Persistent Volume Claims are pending using `kubectl get pvc`**, because unbound storage can keep the pod in Pending state.

6. Finally, I would **check scheduler logs or events and fix the root cause**, such as adding more nodes, adjusting resources, or correcting configuration. 


## 28. A Pod is in CrashLoopBackOff — what steps will you take to debug?
1. First, I would **describe the pod using `kubectl describe pod <pod_name>`**, to check events and see the restart reason (exit codes, probe failures), because Kubernetes usually shows why the container is crashing.

2. Then, I would **check container logs using `kubectl logs <pod_name>` or `kubectl logs <pod_name> --previous`**, to identify application errors from the current or last failed run.

3. Next, I would **verify configuration like environment variables, secrets, and config maps using `kubectl describe pod <pod_name>`**, because wrong or missing config is a common cause of crashes.

4. After that, I would **exec into the pod using `kubectl exec -it <pod_name> -- /bin/sh`**, to inspect the environment and validate files, dependencies, or runtime behavior.

5. I would also **check resource limits and OOM issues using `kubectl describe pod <pod_name>`**, to see if the container is being killed due to memory/CPU constraints.

6. Finally, I would **review liveness/readiness probes and recent deployments using `kubectl rollout history deployment/<name>`**, to ensure probes are correct and recent changes didn’t introduce the issue. 

## 29. A Pod is running but the application is not accessible — how will you investigate?
1. First, I would **check pod status and logs using `kubectl get pods` and `kubectl logs <pod_name>`**, to confirm the application started correctly and there are no runtime errors.

2. Then, I would **exec into the pod using `kubectl exec -it <pod_name> -- /bin/sh` and test locally with `curl localhost:<port>`**, to verify if the app is running and reachable inside the pod.

3. Next, I would **check if the application is listening on the correct port using `netstat -tulnp` or `ss -tulnp` inside the pod**, because if it’s not listening, external access won’t work.

4. After that, I would **verify the Service configuration using `kubectl get svc` and `kubectl describe svc <service_name>`**, to ensure correct port, targetPort, and selector are defined.

5. I would also **check endpoints using `kubectl get endpoints <service_name>`**, to confirm the service is correctly mapped to the pod IPs.

6. Finally, I would **test connectivity via service or ingress using `kubectl port-forward <pod_name> <local_port>:<container_port>` or check ingress rules**, to ensure there are no networking or routing issues. 

## 30. Pods are not getting scheduled on any node — what could be the reasons?
1. First, I would **describe the pod using `kubectl describe pod <pod_name>`**, to check scheduling events and errors (like “0/3 nodes available”), because Kubernetes clearly states why scheduling failed.

2. Then, I would **check node status using `kubectl get nodes` and `kubectl describe nodes`**, to verify nodes are Ready and have sufficient CPU/memory resources.

3. Next, I would **review resource requests and limits in the pod using `kubectl describe pod <pod_name>`**, because high requests can prevent scheduling if nodes don’t have enough capacity.

4. After that, I would **check node selectors, affinity/anti-affinity rules, and labels using `kubectl describe pod <pod_name>` and `kubectl get nodes --show-labels`**, since mismatches can block scheduling.

5. I would also **inspect taints and tolerations using `kubectl describe nodes` and `kubectl describe pod <pod_name>`**, because pods without proper tolerations cannot be scheduled on tainted nodes.

6. Finally, I would **check Persistent Volume Claims using `kubectl get pvc`**, since unbound PVCs or storage issues can keep pods unscheduled. 

## 31. A node shows NotReady status — how do you troubleshoot it?
1. First, I would **check node status and details using `kubectl get nodes` and `kubectl describe node <node_name>`**, to see conditions (MemoryPressure, DiskPressure, NetworkUnavailable) and recent events.

2. Then, I would **log into the node and check kubelet status using `systemctl status kubelet`**, because kubelet issues are a common reason for a node becoming NotReady.

3. Next, I would **review kubelet logs using `journalctl -u kubelet -f`**, to identify errors related to networking, certificates, or resource issues.

4. After that, I would **check container runtime status (Docker/containerd) using `systemctl status docker` or `systemctl status containerd`**, since kubelet depends on the runtime to manage pods.

5. I would also **verify node resources like CPU, memory, and disk using `top`, `free -m`, and `df -h`**, because resource exhaustion can make the node unhealthy.

6. Finally, I would **check networking (CNI) and connectivity to the control plane using tools like `ping` or `curl`**, and fix the issue or restart services (`systemctl restart kubelet`) to bring the node back to Ready. 

## 32. Application is not accessible through Service — what checks will you perform?
1. First, I would **check if the pods are running and healthy using `kubectl get pods` and `kubectl logs <pod_name>`**, because if pods are not working, the Service won’t have anything to route traffic to.

2. Then, I would **verify the Service configuration using `kubectl get svc` and `kubectl describe svc <service_name>`**, to ensure correct `port`, `targetPort`, and selector are defined.

3. Next, I would **check endpoints using `kubectl get endpoints <service_name>`**, because if endpoints are empty, the Service is not linked to any pods.

4. After that, I would **validate label matching between pods and Service using `kubectl get pods --show-labels`**, since mismatched labels prevent the Service from selecting pods.

5. I would also **test connectivity from inside the cluster using `kubectl exec -it <pod_name> -- curl <service_name>:<port>`**, to confirm internal networking is working.

6. Finally, I would **check kube-proxy, network policies, or firewall rules using `kubectl get networkpolicy`**, to ensure nothing is blocking traffic between Service and pods. 

## 33. Service is working but Ingress is not routing traffic — how do you debug?
1. First, I would **check Ingress resource configuration using `kubectl get ingress` and `kubectl describe ingress <ingress_name>`**, to verify host, path, and backend service mapping are correct.

2. Then, I would **verify that the Ingress controller is running using `kubectl get pods -n <ingress-namespace>`**, because without a controller (like NGINX), Ingress won’t work.

3. Next, I would **check if the service behind Ingress is working using `kubectl get svc` and `kubectl get endpoints <service_name>`**, since Ingress depends on a healthy service.

4. After that, I would **inspect Ingress controller logs using `kubectl logs <ingress-controller-pod> -n <namespace>`**, to identify routing errors or misconfigurations.

5. I would also **verify DNS and host mapping using `nslookup <domain>` or `curl -H "Host: <domain>" <ingress_ip>`**, because incorrect DNS can prevent traffic from reaching Ingress.

6. Finally, I would **check TLS configuration, annotations, and path rules**, ensuring no mismatch in paths, certificates, or rewrite rules is blocking routing. 

## 34. DNS resolution is failing inside Pods — how will you fix it?
1. First, I would **verify DNS resolution inside the pod using `kubectl exec -it <pod_name> -- nslookup kubernetes.default` or `dig kubernetes.default`**, to confirm whether DNS is actually failing.

2. Then, I would **check CoreDNS pods using `kubectl get pods -n kube-system -l k8s-app=kube-dns`**, because DNS in Kubernetes is handled by CoreDNS.

3. Next, I would **inspect CoreDNS logs using `kubectl logs -n kube-system <coredns-pod>`**, to identify errors like upstream DNS issues or configuration problems.

4. After that, I would **verify the CoreDNS service using `kubectl get svc -n kube-system`**, ensuring the ClusterIP is correct and accessible.

5. I would also **check `/etc/resolv.conf` inside the pod using `kubectl exec -it <pod_name> -- cat /etc/resolv.conf`**, to confirm it is pointing to the correct DNS service IP.

6. Finally, I would **restart CoreDNS or fix its configuration (`kubectl rollout restart deployment coredns -n kube-system`)**, and verify networking (CNI) if DNS traffic is being blocked. 


## 35. Pods cannot communicate with each other — what networking checks will you perform?
1. First, I would **verify pod-to-pod connectivity using `kubectl exec -it <pod_name> -- ping <other_pod_ip>` or `curl <service_name>`**, to confirm whether communication is actually failing at network level.

2. Then, I would **check pod IPs and node distribution using `kubectl get pods -o wide`**, because cross-node communication depends on proper CNI networking.

3. Next, I would **verify Services and endpoints using `kubectl get svc` and `kubectl get endpoints <service_name>`**, to ensure pods are correctly exposed and discoverable.

4. After that, I would **check Network Policies using `kubectl get networkpolicy`**, because restrictive policies can block traffic between pods.

5. I would also **inspect CNI plugin status and logs (like Calico/Flannel) using `kubectl get pods -n kube-system` and `kubectl logs <cni-pod>`**, since networking issues often come from CNI failures.

6. Finally, I would **check node-level networking and firewall rules (iptables) and verify routes**, to ensure there is no host-level restriction blocking pod communication. 

## 36. External users cannot access the application — how will you troubleshoot?
1. First, I would **check if the application and pods are running using `kubectl get pods` and `kubectl logs <pod_name>`**, because if the app itself is down, external access will obviously fail.

2. Then, I would **verify the Service configuration using `kubectl get svc` and `kubectl describe svc <service_name>`**, to ensure the correct type (NodePort/LoadBalancer) and ports are exposed.

3. Next, I would **check Ingress configuration using `kubectl get ingress` and `kubectl describe ingress <ingress_name>`**, to confirm proper routing rules and backend service mapping.

4. After that, I would **validate DNS resolution using `nslookup <domain>` or `dig <domain>`**, to ensure the domain is pointing to the correct Load Balancer or Ingress IP.

5. I would also **test connectivity using `curl <external_ip>` or `curl -H "Host: <domain>" <ingress_ip>`**, to isolate whether the issue is DNS, Ingress, or backend.

6. Finally, I would **check firewall rules, security groups, and network policies**, to ensure external traffic is allowed and not being blocked before reaching the cluster. 

## 37. A Deployment rollout failed — how do you rollback safely?
1. First, I would **check rollout status using `kubectl rollout status deployment/<deployment_name>`**, to confirm the failure and understand if it’s stuck or partially rolled out.

2. Then, I would **inspect the Deployment and pod errors using `kubectl describe deployment <deployment_name>` and `kubectl get pods`**, to quickly understand what went wrong.

3. Next, I would **view rollout history using `kubectl rollout history deployment/<deployment_name>`**, to identify available previous stable revisions.

4. After that, I would **rollback to the previous version using `kubectl rollout undo deployment/<deployment_name>`**, to restore the last known stable state immediately.

5. If needed, I would **rollback to a specific revision using `kubectl rollout undo deployment/<deployment_name> --to-revision=<revision_number>`**, for precise control.

6. Finally, I would **verify application health using `kubectl get pods` and `kubectl logs`**, and monitor traffic to ensure the rollback is successful and users are no longer impacted. 

## 38. New Pods are not getting updated image after deployment — what could be wrong?
1. First, I would **check the image configured in the Deployment using `kubectl get deployment <deployment_name> -o yaml`**, to confirm whether the new image tag is actually updated.

2. Then, I would **verify rollout status using `kubectl rollout status deployment/<deployment_name>`**, because the deployment might not have triggered a new rollout.

3. Next, I would **check if the image tag is static like `latest` and imagePullPolicy using `kubectl describe pod <pod_name>`**, since Kubernetes may reuse cached images if `imagePullPolicy` is not set to `Always`.

4. After that, I would **force a rollout restart using `kubectl rollout restart deployment/<deployment_name>`**, to ensure new pods pull the updated image.

5. I would also **check node-level image cache using `crictl images` or `docker images`**, because nodes might still be using old cached images.

6. Finally, I would **verify CI/CD pipeline and image registry**, to ensure the new image was actually built, pushed, and tagged correctly. 

## 39. Rolling update caused downtime — how do you prevent it?
1. First, I would **check the Deployment strategy using `kubectl get deployment <deployment_name> -o yaml`**, to ensure it is set to RollingUpdate and not Recreate, because Recreate causes downtime.

2. Then, I would **configure `maxUnavailable=0` and `maxSurge>0` in the deployment spec**, so no pods are taken down before new ones are ready.

3. Next, I would **ensure readiness probes are correctly configured using `kubectl describe pod <pod_name>`**, because traffic should only go to pods that are fully ready.

4. After that, I would **verify liveness probes and startup time**, to ensure pods are not restarted or marked ready too early.

5. I would also **check resource availability using `kubectl describe nodes`**, because insufficient CPU/memory can delay new pods and cause downtime.

6. Finally, I would **use strategies like canary or blue-green deployments and test with `kubectl rollout status deployment/<deployment_name>`**, to ensure zero downtime during future updates. 

## 40. Multiple Pods are restarting frequently — how will you find the root cause?
1. First, I would **check pod status and restart count using `kubectl get pods`**, to identify which pods are restarting and how frequently, because high restart count indicates instability.

2. Then, I would **describe the pod using `kubectl describe pod <pod_name>`**, to check events and reasons like CrashLoopBackOff, OOMKilled, or probe failures.

3. Next, I would **check container logs using `kubectl logs <pod_name>` and `kubectl logs <pod_name> --previous`**, to capture errors from both current and previous crashes.

4. After that, I would **verify configuration like environment variables, secrets, and config maps using `kubectl describe pod <pod_name>`**, since misconfiguration is a common cause.

5. I would also **check resource limits and usage using `kubectl top pod <pod_name>`**, to see if pods are being killed due to memory/CPU constraints.

6. Finally, I would **review health checks and recent deployments using `kubectl rollout history deployment/<deployment_name>`**, to identify if probe misconfiguration or recent changes triggered the restarts. 

## 41. Health probes are failing — how do you debug liveness/readiness issues?
1. First, I would **check probe configuration using `kubectl describe pod <pod_name>`**, to verify the correct path, port, and type (HTTP/TCP/exec), because wrong configuration is a common cause of failures.

2. Then, I would **test the probe manually inside the pod using `kubectl exec -it <pod_name> -- curl localhost:<port>/<path>`**, to confirm whether the application is actually responding.

3. Next, I would **check container logs using `kubectl logs <pod_name>`**, because probe failures often correlate with application errors or startup issues.

4. After that, I would **review timing parameters like `initialDelaySeconds`, `timeoutSeconds`, and `failureThreshold` in the deployment YAML**, since strict timings can cause premature failures.

5. I would also **check resource usage using `kubectl top pod <pod_name>`**, because CPU/memory pressure can delay responses and fail probes.

6. Finally, I would **verify dependencies and startup behavior**, ensuring the app is fully initialized before probes run, and adjust probe settings accordingly to prevent unnecessary restarts. 

## 42. High CPU usage observed in cluster — how do you identify the root cause?
1. First, I would **confirm high CPU usage at cluster and node level using `kubectl top nodes`**, to identify which nodes are under pressure and whether the issue is widespread or localized.

2. Then, I would **identify high CPU-consuming pods using `kubectl top pods -A --sort-by=cpu`**, to pinpoint which workloads are causing the spike.

3. Next, I would **inspect the specific pods using `kubectl describe pod <pod_name>`**, to check resource limits, requests, and any abnormal behavior.

4. After that, I would **check container-level processes using `kubectl exec -it <pod_name> -- top` or `ps aux --sort=-%cpu`**, to find the exact process consuming CPU.

5. I would also **review application logs using `kubectl logs <pod_name>`**, because high CPU is often caused by bugs, loops, or inefficient code paths.

6. Finally, I would **correlate with traffic and recent deployments using `kubectl rollout history deployment/<deployment_name>`**, and then fix the root cause (optimize code, scale pods, or adjust resources). 

## 43. Memory usage is continuously increasing in Pods — what could be the issue?
1. First, I would **confirm the memory trend using `kubectl top pods -A`**, to verify that memory usage is continuously increasing and not just a temporary spike.

2. Then, I would **check pod details using `kubectl describe pod <pod_name>`**, to see if containers are getting OOMKilled or hitting memory limits.

3. Next, I would **inspect container-level processes using `kubectl exec -it <pod_name> -- top` or `ps aux --sort=-%mem`**, to identify which process is consuming memory.

4. After that, I would **analyze application logs using `kubectl logs <pod_name>`**, because memory leaks or improper resource handling in code are common causes.

5. I would also **check resource limits and requests in the deployment YAML**, to ensure they are properly set and not too low or missing.

6. Finally, I would **correlate with workload and recent changes using `kubectl rollout history deployment/<deployment_name>`**, and fix the root cause (like memory leaks), or scale resources if required. 

## 44. Cluster autoscaling is not working — how do you debug it?
1. First, I would **check Cluster Autoscaler pods using `kubectl get pods -n kube-system`**, to confirm the autoscaler is running and not in a failed state.

2. Then, I would **inspect autoscaler logs using `kubectl logs -n kube-system <cluster-autoscaler-pod>`**, to identify errors like insufficient permissions, scaling limits, or node group issues.

3. Next, I would **verify pending pods using `kubectl get pods --field-selector=status.phase=Pending`**, because autoscaling only triggers when pods cannot be scheduled due to lack of resources.

4. After that, I would **describe pending pods using `kubectl describe pod <pod_name>`**, to confirm they are unschedulable due to resource constraints and not due to config issues like taints or affinity.

5. I would also **check node group configuration and limits (min/max size)** in the cloud provider, to ensure scaling is allowed and not restricted.

6. Finally, I would **verify IAM roles/permissions and resource quotas**, ensuring the autoscaler has permission to create nodes and that there are no quota or capacity issues blocking scaling. 

## 45. Horizontal Pod Autoscaler (HPA) is not scaling Pods — what could be wrong?
1. First, I would **check HPA status using `kubectl get hpa` and `kubectl describe hpa <hpa_name>`**, to see current metrics, desired replicas, and any errors.

2. Then, I would **verify metrics availability using `kubectl top pods`**, because HPA depends on metrics-server; if metrics are missing, scaling won’t work.

3. Next, I would **check if metrics are crossing thresholds defined in HPA (like CPU%) using `kubectl describe hpa <hpa_name>`**, since scaling only triggers when thresholds are exceeded.

4. After that, I would **verify resource requests are set in the pod spec using `kubectl describe pod <pod_name>`**, because HPA requires CPU/memory requests to calculate utilization.

5. I would also **check min/max replica limits using `kubectl describe hpa <hpa_name>`**, to ensure scaling is not restricted by configuration.

6. Finally, I would **review events and logs of metrics-server using `kubectl get pods -n kube-system` and `kubectl logs <metrics-server-pod> -n kube-system`**, to fix any underlying metrics or configuration issues. 

## 46. Resource limits are not being enforced — how do you troubleshoot?
1. First, I would **verify that resource limits are actually defined in the pod spec using `kubectl get pod <pod_name> -o yaml`**, because if limits are missing, Kubernetes cannot enforce them.

2. Then, I would **describe the pod using `kubectl describe pod <pod_name>`**, to confirm the applied requests and limits and check for any related warnings or events.

3. Next, I would **check actual resource usage using `kubectl top pod <pod_name>`**, to see if the container is exceeding expected usage and whether throttling or OOM kills are happening.

4. After that, I would **verify the container runtime and node configuration (cgroups) on the node using `docker inspect <container_id>` or `crictl inspect <container_id>`**, because limits are enforced at the runtime level.

5. I would also **check for LimitRange or ResourceQuota using `kubectl get limitrange` and `kubectl get resourcequota`**, since cluster policies can override or affect resource behavior.

6. Finally, I would **inspect kubelet and node logs using `journalctl -u kubelet`**, to ensure there are no issues with enforcement, and fix any misconfiguration or missing limits. 

## 47. Persistent Volume (PV) is not getting attached to Pod — how do you debug?
1. First, I would **check PVC status using `kubectl get pvc`**, to see if it is `Pending` or `Bound`, because if it’s not bound, the Pod cannot use the volume.

2. Then, I would **describe the PVC using `kubectl describe pvc <pvc_name>`**, to identify issues like no matching PV, storage class problems, or provisioning errors.

3. Next, I would **check available PVs using `kubectl get pv`**, to ensure a suitable volume exists and matches size, access modes, and storage class.

4. After that, I would **verify StorageClass configuration using `kubectl get sc` and `kubectl describe sc <sc_name>`**, since dynamic provisioning depends on correct storage class settings.

5. I would also **describe the Pod using `kubectl describe pod <pod_name>`**, to check events for volume attachment errors (like permission issues or attach failures).

6. Finally, I would **check cloud provider or CSI driver logs using `kubectl get pods -n kube-system` and `kubectl logs <csi-pod>`**, to identify underlying issues with volume provisioning or attachment. 

## 48. Data is lost after Pod restart — what could be the reason?
1. First, I would **check if the application is writing data to the container filesystem using `kubectl exec -it <pod_name> -- ls <path>`**, because container storage is ephemeral and data is lost on restart.

2. Then, I would **verify if a volume is configured in the pod using `kubectl get pod <pod_name> -o yaml`**, to ensure persistent storage is actually attached.

3. Next, I would **check if the correct volume type (like PVC) is used instead of `emptyDir`**, because `emptyDir` is temporary and gets deleted when the pod restarts.

4. After that, I would **inspect PVC and PV status using `kubectl get pvc` and `kubectl get pv`**, to confirm that storage is properly bound and available.

5. I would also **verify volume mount paths using `kubectl describe pod <pod_name>`**, to ensure the application is writing data to the mounted persistent path.

6. Finally, I would **fix the configuration by using Persistent Volumes and correct mount paths**, and test by restarting the pod to confirm data persists. 

## 49. Storage mounting errors occurring in Pods — how do you fix them?
1. First, I would **check pod events using `kubectl describe pod <pod_name>`**, to see exact mount error messages (like “volume not found”, “permission denied”, or “attach failed”), because events give the root clue.

2. Then, I would **verify PVC status using `kubectl get pvc` and `kubectl describe pvc <pvc_name>`**, to ensure it is `Bound` and not stuck in `Pending`.

3. Next, I would **check PV details using `kubectl get pv` and `kubectl describe pv <pv_name>`**, to confirm it matches size, access modes, and storage class required by the PVC.

4. After that, I would **verify StorageClass configuration using `kubectl get sc` and `kubectl describe sc <sc_name>`**, since incorrect provisioner or parameters can cause mounting failures.

5. I would also **check CSI driver or cloud provider logs using `kubectl get pods -n kube-system` and `kubectl logs <csi-pod>`**, because storage plugins often fail during attach/mount operations.

6. Finally, I would **check permissions and mount paths inside the pod using `kubectl exec -it <pod_name> -- ls -l <mount_path>`**, and fix any configuration, access mode, or permission issues before retrying. 

## 50. Volume permissions issue inside container — how do you resolve it?
1. First, I would **verify the permission error by exec-ing into the pod using `kubectl exec -it <pod_name> -- ls -l <mount_path>`**, to confirm whether the application user lacks read/write access.

2. Then, I would **check the user running inside the container using `kubectl exec -it <pod_name> -- id`**, because mismatched UID/GID between container and volume often causes permission issues.

3. Next, I would **inspect the pod security context using `kubectl get pod <pod_name> -o yaml`**, to see if `runAsUser`, `runAsGroup`, or `fsGroup` are set correctly.

4. After that, I would **fix permissions by setting `fsGroup` in the pod spec**, so Kubernetes automatically adjusts volume ownership for the container user.

## 51. StatefulSet Pods are not coming up in order — how do you troubleshoot?
1. First, I would **check StatefulSet status using `kubectl get statefulset` and `kubectl describe statefulset <name>`**, to see current replicas and any errors, because StatefulSets follow strict ordered startup.

2. Then, I would **check pods with `kubectl get pods -l app=<label> -o wide`**, to verify which pod (like `pod-0`, `pod-1`) is stuck, since higher index pods won’t start until lower ones are ready.

3. Next, I would **inspect the first failing pod using `kubectl describe pod <pod_name>`**, because if `pod-0` fails, others will not be created.

4. After that, I would **check logs using `kubectl logs <pod_name>`**, to identify application or startup issues preventing readiness.

5. I would also **verify Persistent Volume Claims using `kubectl get pvc`**, since StatefulSets depend on stable storage and pending PVCs can block pod startup.

6. Finally, I would **check readiness probes and configuration**, ensuring the pod becomes Ready (`kubectl describe pod <pod_name>`), because StatefulSets only proceed to the next pod when the previous one is fully ready. 

## 52. Kubernetes secrets are not accessible inside Pod — what could be wrong?
1. First, I would **verify the Secret exists using `kubectl get secrets`**, to ensure it is created in the same namespace as the Pod, because cross-namespace access is not allowed.

2. Then, I would **check pod configuration using `kubectl describe pod <pod_name>`**, to confirm the Secret is correctly referenced (as env or volume).

3. Next, I would **validate the Secret keys using `kubectl get secret <secret_name> -o yaml`**, to ensure the key names match what the Pod is trying to access.

4. After that, I would **exec into the pod using `kubectl exec -it <pod_name> -- env` or `kubectl exec -it <pod_name> -- ls /path/to/secret`**, to verify if the Secret is actually mounted or injected.

5. I would also **check for typos or wrong references in the Deployment YAML**, since incorrect secret names or keys will result in missing values.

6. Finally, I would **check RBAC permissions and restart the pod using `kubectl delete pod <pod_name>`**, to ensure the Secret is properly loaded and accessible after fix. 

## 53. RBAC permission errors occurring — how do you debug access issues?
1. First, I would **reproduce and identify the exact permission error using the failing command (e.g., `kubectl get pods`) and note the “forbidden” message**, because it tells which user/service account and resource is denied.

2. Then, I would **check which ServiceAccount is being used using `kubectl describe pod <pod_name>` or `kubectl config view`**, to confirm the identity making the request.

3. Next, I would **verify permissions using `kubectl auth can-i <verb> <resource> --as=<user_or_sa> -n <namespace>`**, to quickly test whether access is allowed or denied.

4. After that, I would **inspect Roles and ClusterRoles using `kubectl get roles,clusterroles` and `kubectl describe role <role_name>`**, to ensure required permissions are defined.

5. I would also **check RoleBindings and ClusterRoleBindings using `kubectl get rolebindings,clusterrolebindings`**, to confirm the role is correctly attached to the user or ServiceAccount.

6. Finally, I would **fix the RBAC configuration (update role/rolebinding) and re-test access using `kubectl auth can-i`**, ensuring proper least-privilege access is granted. 

## 54. Cluster API server is slow or not responding — what steps will you take?
1. First, I would **check API server health and latency using `kubectl get --raw='/healthz'` and `kubectl get --raw='/metrics'`**, to confirm if it’s responsive and identify delays.

2. Then, I would **check API server pod status using `kubectl get pods -n kube-system | grep apiserver`**, to ensure it is running and not restarting.

3. Next, I would **inspect API server logs using `kubectl logs -n kube-system <apiserver-pod>`**, to identify errors like timeouts, etcd issues, or authentication problems.

4. After that, I would **check etcd health using `kubectl get pods -n kube-system | grep etcd` and `kubectl logs <etcd-pod> -n kube-system`**, because API server depends heavily on etcd performance.

5. I would also **verify node resource usage using `kubectl top nodes`**, since high CPU/memory on control plane nodes can slow down the API server.

6. Finally, I would **check network latency and connectivity (`ping`, `curl`) and restart components if needed**, ensuring the control plane is stable and responsive. 

## 55. etcd storage is full — how do you handle this situation?
1. First, I would **confirm etcd is full by checking logs using `kubectl logs -n kube-system <etcd-pod>` and disk usage using `df -h` on the control plane node**, because etcd becomes unavailable when storage quota is exceeded.

2. Then, I would **check etcd database size using `ETCDCTL_API=3 etcdctl endpoint status --write-out=table`**, to see how large the DB is and confirm it needs cleanup.

3. Next, I would **remove unnecessary or stale Kubernetes objects (like old resources, events) using `kubectl get events --all-namespaces` and cleanup if needed**, because excessive objects increase etcd size.

4. After that, I would **compact the etcd database using `ETCDCTL_API=3 etcdctl compact <revision>`**, to remove old versions of data.

5. I would also **defragment etcd using `ETCDCTL_API=3 etcdctl defrag`**, to reclaim disk space after compaction.

6. Finally, I would **increase disk size or configure monitoring/alerts**, ensuring etcd has enough capacity and future issues are prevented. 

## 56. How do you troubleshoot a complete application outage in a Kubernetes cluster?
1. First, I would **confirm the outage and check cluster health using `kubectl get nodes` and `kubectl get pods -A`**, to see if nodes or multiple pods are down, because this tells whether it’s cluster-wide or app-specific.

2. Then, I would **check recent changes using `kubectl rollout history deployment/<deployment_name>`**, because outages are often caused by recent deployments or configuration changes.

3. Next, I would **inspect failing pods using `kubectl describe pod <pod_name>` and `kubectl logs <pod_name>`**, to identify errors like crashes, misconfigurations, or dependency failures.

4. After that, I would **verify Service and Ingress using `kubectl get svc`, `kubectl describe svc <service_name>`, and `kubectl get ingress`**, to ensure traffic routing is working correctly.

5. I would also **check DNS and networking using `kubectl exec -it <pod_name> -- nslookup <service>` or `curl <service_name>`**, because networking or DNS issues can break communication.

6. Finally, I would **check infrastructure components like API server, etcd, and nodes (`kubectl top nodes`, logs)**, and take action such as rollback (`kubectl rollout undo deployment/<deployment_name>`) or scaling to restore service quickly. 

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

