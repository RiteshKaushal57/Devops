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
An Argo CD Project is a logical boundary that defines a set of policies and constraints applied to applications.
It controls which Git repositories an application can source manifests from, which Kubernetes clusters and namespaces it can deploy to, what cluster-scoped resources it is allowed to manage, and which users or teams have access through RBAC.
Applications assigned to a project must comply with these rules, making Projects a governance and security mechanism rather than a runtime or execution unit.

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



ssh -i ~/.ssh/devops.pem ubuntu@34.207.243.103

VxYaIiZJyuDxwM1s