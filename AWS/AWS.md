## What is AWS Elastic Container Registry (ECR)?
AWS ECR is a fully managed service where we store our container images. It works just like Docker Hub, but it’s more secure and tightly integrated with AWS services like ECS, EKS, and CodePipeline. Developers push images to ECR and Kubernetes or ECS can pull those images automatically during deployment.

## What is Elastic Container Service (ECS)?
AWS ECS (Elastic Container Service) is a fully managed service that runs and scales Docker containers. It takes care of scheduling, running, and monitoring containers, so developers don’t have to manage servers manually. It can run on EC2 or in a serverless way using AWS Fargate.

## What is AWS Elastic Kubernetes Service (EKS)?
AWS EKS is a managed Kubernetes service by Amazon. It helps you run Kubernetes clusters without having to install or manage the Kubernetes control plane (the master nodes). You just focus on your applications (pods, deployments, services), and AWS handles the heavy lifting — like control plane setup, security, and scaling.

## ECS vs EKS vs Kubernetes