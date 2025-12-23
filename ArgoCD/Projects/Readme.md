Great, theory + hands-on is exactly how Argo CD really clicks. Below are **practical Argo CD projects**, ordered from **beginner → advanced**, each with **what you’ll learn** and **clear deliverables** so you know when you’ve actually “done” it.

---

## 1️⃣ GitOps Deployment of a Single Microservice (Beginner)

**Project**

* Deploy a simple app (Node.js / Java / Nginx) to Kubernetes using Argo CD.

**What you do**

* Create a Git repo with:

  * `deployment.yaml`
  * `service.yaml`
* Install Argo CD on a local cluster (kind / minikube).
* Create an Argo CD Application pointing to your repo.
* Enable **auto-sync** and **self-heal**.

**You will learn**

* How Argo CD watches Git
* Sync vs Auto-sync
* Desired state vs live state
* Self-healing (delete pod → Argo recreates it)

**Deliverable**

* App visible as **Healthy & Synced** in Argo CD UI.

---

## 2️⃣ Multi-Environment GitOps (Dev / QA / Prod)

**Project**

* Same application deployed to **multiple environments**.

**What you do**

* Repo structure:

  ```
  app/
    base/
    overlays/
      dev/
      qa/
      prod/
  ```

  (Kustomize or Helm)
* Create **3 Argo CD Applications** (dev, qa, prod).
* Use different replicas, image tags per environment.

**You will learn**

* Environment separation
* Kustomize / Helm with Argo CD
* How Git controls promotion

**Deliverable**

* Dev auto-sync enabled
* Prod manual sync only

---

## 3️⃣ Argo CD with Helm Chart (Very Important for Real Jobs)

**Project**

* Deploy an application using a **Helm chart** via Argo CD.

**What you do**

* Use a Helm chart (your own or public).
* Modify `values.yaml`.
* Deploy via Argo CD Application with:

  * Helm parameters
  * Value overrides

**You will learn**

* Helm + Argo CD integration
* How Argo renders Helm templates
* Why companies prefer Helm + Argo

**Deliverable**

* Helm-based app fully managed by Argo CD.

---

## 4️⃣ Image Auto-Update using Argo CD Image Updater

**Project**

* Automatically update container images when a new tag is pushed.

**What you do**

* Build & push Docker images (v1, v2).
* Configure **Argo CD Image Updater**.
* Use image aliases and update strategies.

**You will learn**

* Image automation without CI triggering deploy
* GitOps image version control
* Semver & latest strategies

**Deliverable**

* New image tag → Git updated → Argo deploys automatically.

---

## 5️⃣ App-of-Apps Pattern (Production-Grade)

**Project**

* Manage **multiple applications** using a single parent app.

**What you do**

* Create a root Argo CD app that deploys:

  * frontend
  * backend
  * database
* Each app has its own folder and manifests.

**You will learn**

* Scalable GitOps design
* How large teams manage dozens of services
* Clean separation of concerns

**Deliverable**

* One Argo app controlling all others.

---

## 6️⃣ Sync Waves & Ordering (Intermediate)

**Project**

* Deploy apps in a strict order.

**What you do**

* Database → Backend → Frontend
* Use **sync waves** annotations.
* Simulate failure and recovery.

**You will learn**

* Deployment orchestration
* Dependency handling in GitOps
* Real-world rollout behavior

**Deliverable**

* Controlled, ordered deployments.

---

## 7️⃣ Argo CD RBAC & Multi-Team Setup (Advanced)

**Project**

* Simulate multiple teams using Argo CD.

**What you do**

* Create Argo CD Projects:

  * team-A
  * team-B
* Restrict:

  * namespaces
  * Git repos
  * cluster access
* Create read-only vs admin roles.

**You will learn**

* Enterprise-level Argo CD security
* Project isolation
* RBAC best practices

**Deliverable**

* Teams cannot access each other’s apps.

---

## 8️⃣ CI + Argo CD End-to-End GitOps Pipeline (Job-Ready Project)

**Project**

* Full pipeline from code commit → production.

**Flow**

```
Code push → CI builds image → Git updated → Argo CD deploys
```

**What you use**

* GitHub Actions / Jenkins
* Docker
* Argo CD

**You will learn**

* Real DevOps GitOps workflow
* Why Argo CD replaces CD pipelines
* Industry-standard setup

**Deliverable**

* Zero manual kubectl deploys.
