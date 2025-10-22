# Deploy a 2048 game app on AWS EKS using Ingress and ALB. This project demonstrates setting up a Kubernetes cluster, configuring services, and creating an Ingress controller for external access.

### Step 1: Install kubectl  
KubeCTL is used to interact with the kubernetes cluster.
```
https://kubernetes.io/docs/tasks/tools/
```

### Step 2: Install eksctl
EKSCTL is used to interact with eks clusters.
```
https://docs.aws.amazon.com/eks/latest/eksctl/installation.html
```

### Step 3: Install AWS CLI
A command line tool for working with AWS services.
```
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
```

### Step 4: Run aws configure command to set up AWS CLI by storing your Access Key, Secret Key, default region, and output format so you can securely interact with AWS services from your terminal.
```
aws configure
```

### Step 5: Create EKS Cluster
```
eksctl create cluster --name your-cluster-name --region us-east-1 --fargate
```

**What this command does**
This command **creates a fully managed EKS (Elastic Kubernetes Service) cluster** named `your-cluster-name` in the AWS region `us-east-1`.
The flag `--fargate` tells AWS to use **AWS Fargate**, which means your pods (containers) will run on **serverless compute** — you don’t need to manage EC2 instances or node groups manually.

**Why we use this command**
To run any Kubernetes app, you need a **cluster** — a combination of:
* **Control Plane** → manages everything (AWS manages this part)
* **Worker Nodes** → actually run your containers (you usually manage these)

* It automatically creates all required **IAM roles, networking, and node groups (or Fargate profiles)**.
* It gives you a **ready-to-use Kubernetes cluster** integrated with AWS.
* The `--fargate` option eliminates node management — AWS allocates compute resources automatically.

**How it works internally**
1. **Authentication with AWS:**
   `eksctl` uses your credentials from `aws configure` to access your AWS account.

2. **Infrastructure setup via CloudFormation:**
   Under the hood, `eksctl` uses **AWS CloudFormation templates** to create:

   * An **EKS control plane**
   * A **VPC (Virtual Private Cloud)** with subnets and security groups
   * **IAM roles** for cluster and Fargate
   * **Fargate profile** (so your pods run on AWS Fargate)

3. **Fargate integration:**
   With `--fargate`, AWS handles compute dynamically — your pods are placed on AWS-managed serverless infrastructure instead of EC2 instances.



*Note:*
*To delete the cluster*
```
eksctl delete cluster --name your-cluster-name --region us-east-1
```

### Step 6: Connect your local machine to the cluster you just created.
```
aws eks update-kubeconfig --name your-cluster-name --region us-east-1
```

**What this command does**
This command **connects your local `kubectl` to your AWS EKS cluster**.
It updates your **Kubeconfig file** (usually located at `~/.kube/config`) so that `kubectl` knows:
* which cluster to talk to (`your-cluster-name`)
* where that cluster is hosted (`us-east-1`)
* which credentials to use (your AWS credentials)

After running this, you can use Kubernetes commands like:
```
kubectl get nodes
```
to directly talk to your EKS cluster.

## Deploy 2048 pod using the deployment
### Step 7: Create Fargate profile
```
eksctl create fargateprofile \
    --cluster your-cluster-name \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048
```

**What this command does**
This command **creates a Fargate profile** for your EKS cluster named `your-cluster-name`.
A **Fargate profile** is like a *rulebook* that tells AWS:

> “Whenever a pod is created inside this namespace (`game-2048`), run it on Fargate — not on EC2 nodes.”

So basically:

* It connects your Kubernetes **namespace** (game-2048)
* With **Fargate compute resources** inside AWS.

From now on, any pod you deploy inside that namespace automatically runs in a **serverless environment** (no EC2 setup needed).

**Why we use this command**
When you create an EKS cluster with `--fargate`, AWS enables Fargate support for the cluster,
but it doesn’t automatically know **which pods** should use Fargate.

That’s where this command comes in.
You need to define a **Fargate profile** to:

1. Tell AWS which **namespace(s)** or **pod labels** to run on Fargate.
2. Separate workloads — for example,

   * run `game-2048` namespace pods on Fargate,
   * but run other system pods on EC2 nodes.

This helps you mix and match — using Fargate for some parts and EC2 for others, depending on your use case.

### Step 8: Deploy the deployment, service and Ingress
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

**What this command does**

This command **deploys the 2048 game application** into your EKS cluster by applying a YAML configuration file that defines three things:

1. A **Deployment** → describes how many pods to run and which container image to use.  
2. A **Service** → exposes those pods inside the cluster.  
3. An **Ingress** → connects your app to the outside world using a **Load Balancer (ALB)**.

### Step 9: Connect your EKS cluster to AWS IAM using an OIDC provider

```
eksctl utils associate-iam-oidc-provider --cluster your-cluster-name --approve
```

**What this command does**

This command **connects your EKS cluster to AWS IAM** using an **OIDC provider (OpenID Connect)**.

In simple words:

> It allows Kubernetes service accounts inside your cluster to securely talk to AWS services (like Load Balancer, S3, CloudWatch, etc.) *without* using access keys.

So this command sets up a **trust bridge** between your EKS cluster and AWS Identity & Access Management (IAM).

**Why we use this command**

By default, your EKS pods (applications running in the cluster) **don’t have AWS permissions** — they can’t create or access AWS resources like a Load Balancer, even if you need them to.

When you install the **AWS Load Balancer Controller**, it needs permission to:

* Create and manage ALBs  
* Attach target groups  
* Configure listeners and rules

But we don’t want to give broad permissions to the whole cluster.
So, AWS uses **IAM Roles for Service Accounts (IRSA)** — a secure way to assign specific permissions to certain pods.
This works only if your cluster is connected to **OIDC (OpenID Connect)**. Hence, we run this command to set that connection.

## Setup alb
### Step 10: Download IAM policy
```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

**What this command does**

This command **downloads a JSON file** (called `iam_policy.json`) from the **official AWS Load Balancer Controller GitHub repo**.

That JSON file contains all the **AWS permissions** (IAM policy) that the **Load Balancer Controller** needs to:

* Create and manage **Application Load Balancers (ALBs)**
* Attach target groups
* Create listeners and routing rules
* Manage security groups and network interfaces

**Why we use this command**

When you deploy the **AWS Load Balancer Controller** in your EKS cluster, it runs as a **Kubernetes pod**.
That pod needs permission to talk to AWS and manage load balancers on your behalf.

Without permissions, it can’t:

* Create ALBs when you define Ingress objects.  
* Register your app pods as targets.  
* Update listener rules when routes change.

So before installing the controller, we **must** give it proper permissions — and that’s what this policy defines.

### Step 11: Create IAM Policy
```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

**What this command does**

This command **creates a new IAM policy inside your AWS account** using the rules defined in the `iam_policy.json` file.

This policy contains detailed permissions like:

* `elasticloadbalancing:*` → allows creating ALBs, listeners, target groups  
* `ec2:*` → allows managing security groups and network interfaces  
* `iam:List*` → allows reading IAM-related info needed for configuration

**Why we use this command**

When the **AWS Load Balancer Controller** runs inside your Kubernetes cluster, it acts on behalf of you to create AWS resources.
But AWS services are **secure by design** — they never allow actions unless the requester has explicit permission.

So we create this **IAM policy** because:

1. It defines *what actions* the controller is allowed to perform (like creating ALBs).  
2. It ensures *least privilege access* — meaning the controller can only do what’s in this policy.  
3. It will later be attached to a **role** that’s linked to the controller’s Kubernetes Service Account through IRSA (IAM Roles for Service Accounts).

### Step 12: Create IAM Role
```
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

**What this command does**

This command **creates an IAM Role** and a **Kubernetes Service Account** that are linked together.

This special connection is called **IRSA** — *IAM Roles for Service Accounts*.
It lets Kubernetes pods assume an AWS IAM Role **securely without using access keys**.

*It creates a “bridge” between your AWS IAM role (which has permissions) and the Load Balancer Controller pod (which needs those permissions).*

After this command, your EKS cluster will have:

* A new **Kubernetes service account** → `aws-load-balancer-controller` in the `kube-system` namespace.
* A new **IAM role** → `AmazonEKSLoadBalancerControllerRole` with the permissions defined in your earlier policy (`AWSLoadBalancerControllerIAMPolicy`).

**Why we use this command**

In Kubernetes, every pod runs isolated — it doesn’t automatically know about AWS.
The **AWS Load Balancer Controller** pod, however, needs to talk to AWS APIs (to create load balancers, listeners, etc.).

If we gave it **Access Keys** manually, it’d be insecure and hard to manage.
So instead, AWS provides **IRSA**, which allows:

1. **Fine-grained permissions** — each pod gets only the permissions it needs.
2. **No secret keys** stored inside pods.
3. **Automatic identity management** — the pod uses the IAM role attached to its service account.

## Deploy ALB controller
### Step 13: Add helm repo
```
helm repo add eks https://aws.github.io/eks-charts
```

**What this command does**

This command **adds the official AWS EKS Helm chart repository (like the Load Balancer Controller, EBS CSI driver, CloudWatch agent, etc.)** to your local Helm configuration.


**WHY — Why we use this command**

In Kubernetes, installing complex applications (like controllers or dashboards) manually using multiple YAML files can be tedious.
That’s where **Helm** comes in — it acts like a *“package manager”* for Kubernetes (just like npm for Node.js or pip for Python).

You use this command because:

1. The **AWS Load Balancer Controller** is packaged as a Helm chart.
2. Helm needs to know **where to find** that chart — and this repo (`https://aws.github.io/eks-charts`) hosts it.
3. Once added, you can easily install, update, or remove AWS components with a single command instead of managing dozens of YAML files.

### Step 14: Update the repo
```
helm repo update eks
```

**What this command does**

This command tells Helm to **fetch the latest chart information** from the `eks` repository you added earlier.

It updates the **local cache** of Helm so it knows:

* The most recent chart versions
* Any new charts added to the repo
* Any updates or bug fixes

### Step 15: Install AWS Load Balancer Controller
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id>
```

**What this command does**

This command **installs the AWS Load Balancer Controller** into your EKS cluster using Helm.

The Load Balancer Controller:

* Watches your **Ingress** and **Service** resources in Kubernetes.
* Automatically **creates, updates, or deletes** AWS **Application Load Balancers (ALBs)** based on them.
* Manages routing between external traffic (internet) and your Kubernetes pods.

**Why we use this command**

Earlier, you deployed your **2048 app** and created an **Ingress** resource — but it didn’t get an external address because Kubernetes didn’t have a controller to handle ALB creation.

By installing this controller, you’re giving Kubernetes the ability to:

* Understand AWS-specific Ingress annotations.
* Create an **Application Load Balancer** automatically when you define an Ingress.
* Manage DNS, target groups, listeners, and rules — all dynamically.

### Step 16: Verify that the deployments are running
```
kubectl get deployment -n kube-system aws-load-balancer-controller
```

**What this command does**

This command checks whether the **AWS Load Balancer Controller** is **successfully running** inside the **`kube-system` namespace**.
It lists all the details about that **deployment**, including its **current status, desired replicas, and available pods**.

**Why we use this command**

We use this command to **verify that our controller is working properly** before exposing our app to the internet.
If this controller isn’t running, then **Ingress and Load Balancer setup won’t work**, meaning users outside AWS can’t access your app (like your 2048 game).

So, this is basically a **health check** step before testing your app externally.

### Step 17: Run the command and search the address in browser  
```
kubectl get ingress -n game-2048
```

**What this command does**

This command checks the **Ingress resource** created for your 2048 app — specifically inside the **`game-2048` namespace**.
Ingress is like a **doorway** that lets external users access your app running inside Kubernetes using a **public URL (DNS name)**.

So, this command helps you find that **public address** (the “door”) to open your game in a browser.


**Why we use this command**

We use it to **verify that our Ingress is successfully linked to an AWS Application Load Balancer (ALB)**.
When everything is set up correctly, the command output will show an **“ADDRESS”** — this is your **public DNS** created by AWS.

You can copy that address, paste it in your browser, and **access your Kubernetes app from anywhere in the world** 🌍.
If the address column is empty, it usually means the ALB is still being created or the controller setup isn’t complete.
