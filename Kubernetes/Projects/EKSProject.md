## Deploy a 2048 game app on AWS EKS using Ingress and ALB. This project demonstrates setting up a Kubernetes cluster, configuring services, and creating an Ingress controller for external access.

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
eksctl create cluster --name demo-cluster --region us-east-1 --fargate
```

**What this command does**
This command **creates a fully managed EKS (Elastic Kubernetes Service) cluster** named `demo-cluster` in the AWS region `us-east-1`.
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
eksctl delete cluster --name demo-cluster --region us-east-1
```

### Step 6: Connect your local machine to the cluster you just created.
```
aws eks update-kubeconfig --name demo-cluster --region us-east-1
```

**What this command does**
This command **connects your local `kubectl` to your AWS EKS cluster**.
It updates your **Kubeconfig file** (usually located at `~/.kube/config`) so that `kubectl` knows:
* which cluster to talk to (`eksproject`)
* where that cluster is hosted (`us-east-1`)
* which credentials to use (your AWS credentials)

After running this, you can use Kubernetes commands like:
```
kubectl get nodes
```
to directly talk to your EKS cluster.

### Deploy 2048 pod using the deployment
### Step 7: Create Fargate profile
```
eksctl create fargateprofile \
    --cluster demo-cluster \
    --region us-east-1 \
    --name alb-sample-app \
    --namespace game-2048
```
Deploy the deployment, service and Ingress
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml

now we ingress does not have address. so need to create ingress controller so that any person of outside the world can access the app.
commands to configure IAM OIDC provider
```
eksctl utils associate-iam-oidc-provider --cluster cluster_name --approve
```

### setup alb
**Download IAM policy**
```
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```
**Create IAM Policy**
```
aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json
```

**Create IAM Role**
```
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

### Deploy ALB controller
**Add helm repo**
```
helm repo add eks https://aws.github.io/eks-charts
```

**Update the repo**
```
helm repo update eks
```

**Install**
```
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id>
```

**Verify that the deployments are running**
```
kubectl get deployment -n kube-system aws-load-balancer-controller
```

**run the command and search the address in browser**  
```
kubectl get ingress -n game-2048
```