## 1. What is GitOps?
**GitOps** is a way of managing applications and infrastructure where **Git is treated as the single source of truth** for what should be running in your system.  
It is used because manual changes, scripts, and direct commands often cause mistakes, inconsistencies, and hard-to-track issues. With GitOps, every change is version-controlled, auditable, and reversible.  
You define the desired state of your system (like Kubernetes YAML files) in a Git repository, and an automated tool such as Argo CD continuously checks the running system and makes sure it matches what’s written in Git—if something changes manually or drifts, the tool either alerts you or automatically corrects it based on the Git configuration.

## 2. GitOps Principles
GitOps is built on four key principles:   
### 1. Declarative
Clearly describe what the system should look like using files like YAML.    
### 2. Versioned & Immutable
Those files are stored in Git so every change is tracked and nothing changes silently.    
### 3. Automated
A tool like Argo CD continuously checks Git and applies changes to the system.    
### 4. Observable
You can see the system’s health, sync status, and history through Git logs and dashboards.

## 3. What is Argo CD?
**Argo CD** is a **GitOps-based continuous delivery tool for Kubernetes** that keeps your applications in sync with what’s defined in Git. **Why it is used** is because manually deploying or updating applications in Kubernetes can lead to mistakes and inconsistencies, while Argo CD ensures the cluster always matches the approved configuration stored in Git, making deployments safer, traceable, and easy to roll back. **How it works** is that Argo CD runs inside the Kubernetes cluster, continuously compares the live state of applications with the desired state stored in a Git repository, and automatically deploys, updates, or corrects the applications whenever a difference is detected, using Git as the single source of truth.

## 4. Explain the ArgoCD Architecture?

1. **API Server**  
   * Acts as the main entry point for users via **UI, CLI, and REST API**.
   * Handles authentication, authorization, and serves the application state data.

2. **Repository Server**  
   * Connects to Git (or Helm/Kustomize repos) to **fetch and validate manifests**.
   * Generates a **kustomized or rendered manifest tree** for the controller to use.

3. **Application Controller**  
   * The **core engine** of Argo CD.
   * Continuously **compares the desired state from Git with the actual cluster state**.
   * Decides which resources need to be created, updated, or deleted, and applies them.
   * Supports **sync strategies** (automatic/manual) and **hooks** (pre-sync, post-sync).

4. **Redis**  
   * Provides caching of **application states** and **cluster resource information** for faster operations.

5. **Dex (optional)**  
   * Handles **Single Sign-On (SSO)** and OAuth integration with GitHub, LDAP, or Google.

6. **Clusters / Target Environments**  
   * One or more **Kubernetes clusters** where Argo CD deploys applications.
   * Can manage **multiple clusters**, with cluster secrets stored securely.

### How it works together:  
User pushes a change to Git → Repository server fetches it → Application controller compares it with the cluster → Differences are applied automatically or manually → API server shows real-time status → Redis caches information → Dex manages authentication.

![ArgoCD Architecture](./ArgoCD%20Architecture.png)

## 5. What are the key ArgoCD Concepts?

### 1. Application
   * **What:** The core Argo CD object representing a deployed app.  
   * **Why:** It links your Git repository, specific path, target Kubernetes cluster, and namespace so Argo CD knows exactly what to deploy and where.   
   * **How:** Once created, Argo CD continuously compares the desired state in Git with the live state in the cluster and applies changes if needed.

### 2. Project
   * **What:** A logical grouping of Applications.   
   * **Why:** It enforces security, access control, and organizational boundaries by defining which teams can deploy to which clusters and namespaces.   
   * **How:** You assign applications to a project, and the project defines allowed repositories, target clusters, and RBAC policies.

### 3. Repositories
   * **What:** Git repositories registered with Argo CD.   
   * **Why:** They store manifests, Helm charts, or Kustomize overlays, which act as the single source of truth for deployments.   
   * **How:** Repos are added to Argo CD, which then monitors them for changes to automatically or manually sync to clusters.

### 4. Health Status
   * **What:** Indicates the current state of an application.   
   * **Why:** Helps quickly identify if an app is running correctly, failing, or still deploying.   

   * **States:**   
     * **Healthy:** Desired state matches live state.   
     * **Degraded:** Application is not functioning correctly (e.g., pod in CrashLoopBackOff).     
     * **Progressing:** Deployment is ongoing.    
     * **Missing:** A resource expected in the cluster is not found.    

   * **How:** Argo CD continuously checks Kubernetes resources and updates the status based on live conditions.

### 5. Rollbacks
   * **What:** Ability to revert to a previous application revision.  
   * **Why:** Ensures quick recovery from failed deployments or unwanted changes.   
   * **How:** Argo CD stores the history of application revisions and allows rolling back to a specific revision through the UI or CLI.

### 6. Auto-Healing
   * **What:** Automatic restoration of resources if drift is detected.   
   * **Why:** Prevents manual changes or accidental deletions from breaking the application.    
   * **How:** If someone deletes a pod or changes a config, Argo CD detects the difference from Git and reapplies the correct state automatically.

### 7. Sync & Sync Policies
   * **What:** Mechanism to apply Git changes to the cluster.   
   * **Why:** Keeps cluster state aligned with Git.   

   * **How:**
     * **Manual Sync:** User triggers deployment via CLI or UI.   
     * **Automatic Sync:** Changes in Git are automatically applied.    
     * **Sync Waves:** Control order of resource application (e.g., database before app).    
     * **Sync Hooks:** Execute scripts/jobs at stages:   
       * PreSync: Before resources are applied   
       * Sync: During resource application   
       * PostSync: After resources applied  
       * SyncFail: On sync failure  

     * **Flags:**  
       * `--prune`: Delete resources not defined in Git.  
       * `--replace`: Force re-create resources instead of patching.   
 
### 8. Sync Options
   * **What:** Additional configurations to customize sync behavior.   
   * **Why:** Provides safety, efficiency, and flexibility when applying resources.   

   * **How:**    
     * **Skip Schema Validation:** Apply resources without validating against Kubernetes schema.    
     * **Auto-Create Namespace:** Creates namespaces automatically if they don’t exist.     
     * **Prune Last:** Deletes old resources at the end of sync to avoid dependency issues.   
     * **Apply Out of Sync Only:** Only applies resources that have changed.   
     * **Respect Ignore Differences:** Avoids overwriting fields that are intentionally ignored.    
     * **Server-Side Apply:** Uses Kubernetes server-side apply to merge changes efficiently.    

### 9. What are ArgoCD Features?

### 1. Projects
An Argo CD Project is a policy container that controls, which Git repos a team can use, which namespaces they can deploy to, which clusters they can access, what they are NOT allowed to touch and which users or teams have access through RBAC.

```
apiVersion: argoproj.io/v1alpha1  # Which Argo CD API rules we’re following
kind: AppProject                  # This YAML defines a Project object
metadata:
  name: team-a-project       # Name of the project
  namespace: argocd          # Namespace where Argo CD is installed
spec:
  description: "Project for Team A applications"  # Optional description
  sourceRepos:                                    # Tells Argo CD where to look for changes
    - https://github.com/org/team-a-apps.git      # Git repositories that contain your Kubernetes manifests
  destinations:                                   # Tells Argo CD where to apply those changes
    - namespace: team-a-*                         # specifies which namespace within that cluster the changes will go to.
      server: https://kubernetes.default.svc      # specifies which Kubernetes cluster the changes will be applied to.
  clusterResourceWhitelist:                       # ArgoCD can manage anything specified under this.
    - group: "*"                                  # Allow all cluster-scoped resources
      kind: "*"                                   # Allow all types of cluster-scoped resources.
  namespaceResourceBlacklist:
    - group: ""                                   # Disallow modifying certain namespace-scoped resources
      kind: "ResourceQuota"                       # ResourceQuota cannot be modified by apps in this project.
  roles:
    - name: team-a-admin                           # Role name
      description: "Full access to project applications"
      policies:                                    # Specifies permissions         
        - "p, proj:team-a-project:admin, applications, *, */*, allow"
      groups:
        - team-a                                   # Maps this role to specific user groups (e.g., team-a).
```
**clusterResourceWhitelist**  
- clusterResourceWhitelist tells Argo CD which cluster-scoped resources applications in this project are allowed to manage.  
- Cluster-scoped resources are resources that exist across the entire cluster, not just in a specific namespace.  
Examples: CustomResourceDefinitions (CRDs), Roles, ClusterRoles, Namespaces.

### 2. App of Apps Pattern
The App of Apps pattern in Argo CD is a way to manage multiple applications (child Applications) from a single parent Application.
Parent Application: An Argo CD Application whose Git repository contains YAMLs for multiple child Applications.
Child Applications: Regular Argo CD Application CRDs, each representing a deployable microservice or component.
Purpose: Instead of managing dozens of applications individually, you manage one parent, and Argo CD automatically creates and syncs all the child Applications.
```
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: parent-app          # The name of the parent Application.
  namespace: argocd         # Usually argocd, the namespace where Argo CD is installed.
spec:
  project: team-a-project   # The Project this Application belongs to, which enforces rules like allowed Git repos and namespaces.
  source:
    repoURL: https://github.com/org/parent-app-repo.git     # Parent Git repository
    path: apps                                              # The folder in parent repository containing the child Application definitions.
    targetRevision: main
  destination:
    server: https://kubernetes.default.svc
    namespace: argocd                                       # Namespace where the parent Application is deployed
  syncPolicy:
    automated:                                              # Enables automatic syncing of child Applications.
      prune: true                                           # Deletes resources that are removed from Git.
      selfHeal: true                                        # Restores resources if someone manually changes or deletes them.

``` 

### 3. Multi-Cluster Management
Multi-Cluster Management in Argo CD is the ability to deploy and manage applications across multiple Kubernetes clusters from a single Argo CD control plane.
Instead of installing Argo CD in every cluster, you can have one central Argo CD instance.
This instance can connect to and manage many clusters, each with its own namespace and workloads.
Useful for enterprises with dev, staging, and production clusters, or multiple regions.

### 4. ApplicationSet
An ApplicationSet in Argo CD is like a master template that automatically creates many Applications for you. Instead of writing separate deployment files for each microservice, environment, or cluster, you define one template with placeholders for things like the service name, Git path, cluster, or namespace. Then you tell Argo CD where to get the actual values, either by giving a list of services, scanning a Git repository, or using all registered clusters. Argo CD reads this ApplicationSet and generates all the Applications automatically, each of which behaves like a normal Argo CD Application—it deploys resources to the cluster, stays in sync with Git, and self-heals if something is changed manually. This makes it very easy for teams to manage dozens or hundreds of microservices consistently, across multiple clusters and environments, without writing repetitive YAML files, saving time and reducing mistakes.

### 5. Config Management 
In Argo CD, Config Management is how the tool handles application configurations and deploys them to Kubernetes clusters in a consistent, automated way. It works with different configuration tools like Helm, Kustomize, or plain YAML files. You store all your application configurations in Git, and Argo CD reads them to figure out what should be deployed. For example, Helm charts allow you to use templates and values files, while Kustomize lets you create overlays for different environments like dev, staging, or production. Argo CD takes these configurations, renders them into proper Kubernetes manifests, and applies them to the target cluster and namespace. It also continuously monitors the cluster to make sure the live state matches what’s in Git, automatically updating or restoring resources if something changes. This approach ensures that applications are deployed consistently across environments, reduces manual errors, and allows teams to manage complex applications at scale in a declarative, version-controlled way.

## 6. What is Argo CD Image Updater?
Argo CD Image Updater is a tool that works alongside Argo CD to automatically update container images in your Kubernetes manifests when new versions are available. Instead of manually editing your YAML files or Helm values to bump image tags, Image Updater monitors container registries for newer versions of the images you use. When it detects a new image, it can automatically update the Git repository with the new version, or optionally trigger a sync in Argo CD so the cluster is updated. This ensures that your applications always run the latest approved images without manual intervention, reducing the risk of running outdated software and simplifying continuous delivery. It integrates seamlessly with Argo CD’s GitOps workflow, so the updates are version-controlled, auditable, and consistent across environments.

When Image Updater finds a new image, it has two ways to “write back” the update:    
1. **Git write-back mode**, Image Updater updates the image tag directly in the Kubernetes manifests stored in the Git repository and creates a new commit. Argo CD then picks up this commit and deploys the change to the cluster. This is considered **best practice in GitOps** because Git remains the single source of truth, every change is version-controlled, auditable, and easy to roll back.      
2. **Argo CD write-back mode**, Image Updater skips Git and directly updates the Argo CD Application resource running in the cluster with the new image version. This is faster and simpler, but it is **imperative** in nature, meaning the change is not recorded in Git, making auditing and rollbacks harder. In real-world production setups, companies usually prefer **Git write-back** for reliability and compliance, while **Argo CD write-back** is sometimes used in non-critical or experimental environments where speed matters more than traceability.

### Semantic Versioning (semver)
semver stands for Semantic Versioning, a way of numbering versions like 1.0.0, 1.0.1, 1.1.0, 2.0.0.  
**Format: MAJOR.MINOR.PATCH**     
**MAJOR** → breaking changes (e.g., 1.x.x → 2.0.0)    
**MINOR** → new features (e.g., 1.1.0 → 1.2.0)    
**PATCH** → bug fixes (e.g., 1.1.1 → 1.1.2)

### Image alias
In Argo CD Image Updater, aliases act as friendly names for container images. Instead of repeatedly referring to a long image name like dockerhub-username/project-name/service-name, you assign a simple alias such as chai-app and use that alias in all Image Updater annotations. This makes configurations easier to read, easier to maintain, and especially useful when working with Helm charts or multiple images, where each image may need different update rules.

## 7. What is Argo Rollouts?
**Argo Rollouts** is a Kubernetes controller used for **advanced and safer application deployments** compared to the default rolling update that Kubernetes provides. Normally, Kubernetes updates applications by gradually replacing old pods with new ones, but it does not give deep control or visibility over how the rollout behaves. Argo Rollouts solves this by allowing teams to deploy applications using **strategies like Canary and Blue-Green**, where new versions are released gradually or alongside the old version and tested before fully going live. It continuously monitors metrics (like error rates or latency) from tools such as Prometheus and can automatically **pause, promote, or roll back** a deployment if something goes wrong. This reduces risk during releases, especially in production systems, and gives teams confidence to ship changes frequently. In simple terms, Argo Rollouts adds **smart decision-making and safety checks** on top of Kubernetes deployments, which is why big companies use it to release applications with minimal downtime and lower chances of failure.

## 8. Argo Rollout Architecture
![Argo Rollout Architecture](Argo%20Rollout%20Architecture.png)

### 1️⃣ Rollout CRD (Top-Left)
* This is the **custom resource** you create instead of a normal Deployment.    
* It defines **how the rollout should happen**:    
  * Canary or Blue-Green strategy    
  * Traffic shift steps    
  * When to pause or analyze      
* Think of this as the **rulebook** for the release.

### 2️⃣ Argo Rollouts Controller (Top-Center)
* This is the **brain of the system**.    
* It continuously watches the Rollout CRD.      
* Based on the rules, it:     
  * Creates new ReplicaSets   
  * Scales old and new pods   
  * Controls traffic movement     
* Kubernetes does the work, but **Argo Rollouts decides how**.   

### 3️⃣ Old ReplicaSet & New ReplicaSet (Middle-Left)
* **Old ReplicaSet** → running the current stable version (old pods).   
* **New ReplicaSet** → running the new version (new pods).    
* Both can exist **at the same time**, which is what makes canary and blue-green possible.    
* This separation makes rollback fast and safe.     

### 4️⃣ Services (Middle)
* Services decide **which pods receive traffic**.    
* In **Blue-Green**:    
  * **Active Service** → stable version    
  * **Preview Service** → new version (for testing)   
* In **Canary**:   
  * Traffic is split between old and new pods using weights.   
* This is how users slowly start hitting the new version.   

### 5️⃣ Traffic Routing & Shift (Center-Bottom)
* Traffic is gradually moved:     
  * 10% → 30% → 50% → 100%    
* This happens through:     
  * Kubernetes Services        
  * Ingress controllers     
  * Service meshes    
* The rollout **does not jump suddenly** to the new version.

### 6️⃣ Analysis & Metrics (Top-Right)
* This is where **safety checks** happen.    
* Argo Rollouts runs **AnalysisRuns** during rollout.     
* It checks metrics from tools like:    
  * Prometheus    
  * Datadog    
  * New Relic    
* Examples:    
  * Error rate too high?   
  * Latency increased?    
* These metrics decide whether to continue or stop.    

### 7️⃣ Promotion or Rollback (Bottom-Right)
* If metrics are **good** → **Promotion**     
  * New version becomes stable    
  * Old version is scaled down     
* If metrics are **bad** → **Rollback**    
  * Traffic goes back to old pods    
  * New pods are scaled down    
* This can happen **automatically**, without human action.   

### 8️⃣ Ingress / Service Mesh (Bottom-Left)
* Tools like:   
  * NGINX     
  * AWS ALB      
  * Istio      
  * Linkerd   
* They help with **advanced traffic control**, like percentage-based routing.    

## 9.  Deployment Strategies

### 1. Blue/Green Deployment
**Blue/Green Deployment** is a deployment strategy used to release a new version of an application **with zero downtime and minimal risk**. In this approach, two identical environments are maintained: **Blue**, which runs the current stable version of the application, and **Green**, which runs the new version. While users continue to access the Blue environment, the Green environment is deployed and tested in isolation using a preview service. Once the new version is verified to be healthy, traffic is switched all at once from Blue to Green by updating the service or load balancer. If any issue is detected after the switch, rollback is very fast because traffic can immediately be redirected back to the Blue environment. In tools like **Argo Rollouts**, this process is automated using services, rollout rules, and health checks, making Blue/Green deployment a safe and reliable strategy commonly used in production systems.

### 2. Canary Deployment
**Canary Deployment** is a deployment strategy where a new version of an application is released **gradually to a small percentage of users** before being rolled out to everyone. Instead of switching all traffic at once, the new version runs alongside the old stable version, and only a fraction of traffic is sent to it initially, such as 5% or 10%. If the application performs well based on metrics like error rate, latency, or user behavior, traffic is slowly increased in steps until the new version serves 100% of users. If any problem is detected at any stage, the rollout is paused or rolled back, limiting the impact. In **Argo Rollouts**, canary deployments are automated and controlled using rollout steps, services, and real-time metrics, making this approach safer than traditional rolling updates and ideal for high-risk or high-traffic production applications.




ssh -i ~/.ssh/devops.pem ubuntu@34.207.243.103

VxYaIiZJyuDxwM1s