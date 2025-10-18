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

aws eks update-kubeconfig --name eksproject --region us-east-1

deploy 2048 pod using the deployment
Create Fargate profile
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