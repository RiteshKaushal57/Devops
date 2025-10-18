### Deploy a 2048 game app on AWS EKS using Ingress and ALB. This project demonstrates setting up a Kubernetes cluster, configuring services, and creating an Ingress controller for external access.

**Step 1: Install kubectl**  
KubeCTL is used to interact with the kubernetes cluster.
```
https://kubernetes.io/docs/tasks/tools/
```

**Step 2: Install eksctl**  
EKSCTL is used to interact with eks clusters.
```
https://docs.aws.amazon.com/eks/latest/eksctl/installation.html
```

**Step 3: Install AWS CLI**  
A command line tool for working with AWS services.
```
https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html
```

**Step 4: Run aws configure command to set up AWS CLI by storing your Access Key, Secret Key, default region, and output format so you can securely interact with AWS services from your terminal.**
```
aws configure
```

**Step 5: Create EKS Cluster**
```
eksctl create cluster --name demo-cluster --region us-east-1 --fargate
```
*Note:*
*To delete the cluster*
```
eksctl delete cluster --name demo-cluster --region us-east-1
```

