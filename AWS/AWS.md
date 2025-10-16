## What is Cloud?
Cloud computing means accessing computing resources—like servers, storage, databases, and software—over the internet instead of managing physical hardware yourself. It offers scalability, cost efficiency, global accessibility, and reliability. For example, using AWS EC2 you can get a server in minutes instead of setting it up physically.

## What is Private Cloud?
Private cloud is when the cloud environment is reserved for just one organization, either hosted on-premises or by a third party. It’s more secure and customizable than public cloud, but also more expensive. Public cloud is shared among many users, while private cloud is dedicated to one.

## What is Public Cloud?
A public cloud is a cloud model where computing resources like servers and storage are owned and managed by a provider such as AWS, Azure, or Google Cloud, and are shared among multiple organizations. You pay as you go, and it’s highly scalable and cost-effective.

## What is Cloud Repatriation?
Moving from public cloud to private cloud is called cloud repatriation.

## Are you using root user?
No, I don’t use the root user for daily operations. I create IAM users with specific permissions for security and follow the principle of least privilege. The root account is reserved only for critical account-level tasks.

*Some services in AWS have elastic as a prefix which shows this particular service can be scaled down or scaled up.*

## What is Virtual Private Cloud (VPC)?
VPC (Virtual Private Cloud) is a logically isolated virtual network in AWS where you can launch and manage your resources like EC2 instances, databases, and load balancers. When a user on the Internet tries to access an application, traffic enters the VPC through the Internet Gateway into the public subnet, where components like a Load Balancer are placed. The Load Balancer then forwards requests to application servers inside the private subnet. Route Tables control traffic flow, Security Groups act as firewalls at the instance level, and NACLs provide subnet-level security.

## What is Internet Gateway?
An Internet Gateway (IGW) in AWS is a network component that enables communication between your VPC and the internet. It allows resources inside your VPC — such as EC2 instances in a public subnet — to send and receive traffic from the internet. Without an Internet Gateway, your instances remain isolated within the VPC and can’t be accessed externally.

## What is Public and Private Subnet?
A **subnet** is a segment of your VPC where you place your AWS resources.

A **public subnet** is a subnet that has a route to the Internet Gateway, so its resources (like EC2 instances) can communicate directly with the internet. This is typically used for web servers or load balancers that need to be publicly accessible.

A **private subnet** is a subnet without a direct route to the internet. Resources here cannot be accessed from the internet directly and are usually used for databases, backend services, or sensitive workloads. To allow outbound internet access (like updates or API calls), a private subnet can use a NAT Gateway.

## What is Load Balancer?
An AWS Load Balancer is a service in Elastic Load Balancing (ELB) that automatically distributes incoming traffic across multiple targets (EC2 instances, containers, IPs, or Lambda functions).
It ensures high availability, fault tolerance, and scalability by preventing any single server from being overloaded.

### Types of Load Balancers in AWS:
**1. Application Load Balancer (ALB):**  
- Works at Layer 7 (HTTP/HTTPS).
- Routes requests based on content (URL path, hostname, query).
- Example: /api → microservice A, /images → microservice B.

**2. Network Load Balancer (NLB):**  
- Works at Layer 4 (TCP/UDP/TLS).
- Handles millions of requests per second with ultra-low latency.
- Example: Ideal for gaming, IoT, or real-time financial apps.  

**3. Gateway Load Balancer (GLB):**  
- Works at Layer 3 (IP level).
- Routes traffic to third-party virtual appliances (firewalls, intrusion detection systems).
- Example: Deploying Palo Alto or Fortinet in AWS.

## What are Route Tables?
Route Tables make sure traffic flows in the right direction, whether it’s between subnets, to the internet, or through other gateways inside your AWS environment. For example, traffic meant for the internet (0.0.0.0/0) is sent to the Internet Gateway, while traffic meant for private resources might go through a NAT Gateway or stay within the VPC.

## What are Security Groups?
Security Groups are used to protect our resources by controlling which traffic is allowed to reach them and which traffic can leave them. Without Security Groups, anyone on the internet could attempt to connect to your instances, creating security risks. They work by letting you define rules based on protocols, ports, and source/destination IP addresses. AWS automatically enforces these rules, allowing only the traffic you permit and blocking everything else.

## What are NACL?
Network Access Control Lists (NACLs) in AWS are another layer of security for your VPC subnets. They are used to control inbound and outbound traffic at the subnet level. Unlike Security Groups, which are attached to individual instances, NACLs apply to the entire subnet and evaluate rules in order, allowing or denying traffic based on IP addresses, protocols, and ports. They provide an extra layer of defense, especially for controlling traffic entering or leaving a private subnet.

## What are Nat Gateways?
NAT Gateways (Network Address Translation Gateways) allows resources in a private subnet to access the internet without exposing their private IP addresses.
Instances in a private subnet don’t have public IPs, so they can’t directly reach the internet. NAT Gateways let these instances communicate externally securely, while keeping their private IP hidden.
They work by translating the private IP of the instance to the NAT Gateway’s public IP when sending requests to the internet. Responses come back through the NAT Gateway, ensuring the instance can access external resources without being directly exposed.

## 🌐 Request Journey Inside a VPC (Step-by-Step)

![Alt text](./VPC.png)  

### 🧭 Step 1: User Sends a Request
- A user types your application URL in their browser.  
- The request comes from the internet and tries to reach your AWS resources.

### 🚪 Step 2: Internet Gateway (IGW)
- The request first hits the Internet Gateway, which acts like the main gate of your VPC.  
- Without this gateway, the internet cannot communicate with anything inside your VPC.

### 🗺️ Step 3: Route Table
Once inside, the request checks the Route Table.
- The Route Table tells the request where to go — for example, toward a Load Balancer or directly to a subnet.
- Think of it as a map that defines all allowed paths for traffic.

### ⚖️ Step 4: Load Balancer
- The Load Balancer receives the incoming traffic and distributes it evenly across multiple EC2 instances (your web servers).  
- This ensures no single server is overloaded and the application stays fast and reliable.

### 🧱 Step 5: Public and Private Subnets
- If your EC2 instances are in a Public Subnet, they can talk directly to the internet (through IGW).  
- If they are in a Private Subnet, they can’t talk to the internet directly. They use a NAT Gateway to make outbound connections securely.

### 🔐 Step 6: NAT Gateway (for Private Subnets)
- The NAT Gateway allows EC2 instances in private subnets to access the internet (for updates or APIs) without exposing them to incoming internet traffic.

### 🧰 Step 7: Security Checks
- Security Groups (SGs) filter traffic to and from individual EC2 instances. They allow or block requests based on defined rules (like allowing HTTP on port 80).  
- Network ACLs (NACLs) act as an extra layer of protection at the subnet level. They control which types of traffic can enter or leave the subnet.

### 💻 Step 8: EC2 Instance Processes the Request
- After passing all the routes and security checks, the request finally reaches the EC2 instance that runs your application.  
- The instance processes the request — for example, fetching data or rendering a web page.

### 🔁 Step 9: Response Sent Back
The EC2 instance sends the response (like your website HTML) back along the same path — through SGs, NACLs, Route Table, and Internet Gateway — back to the user’s browser.

## What is Route53?
Route 53 is DNS (Domain Name System) service that translates domain names into IP addresses.
When a user types your website name into a browser, Route 53 receives that request, looks up the DNS record, and routes the user to the right destination.
It can also perform health checks, so if one server fails, Route 53 automatically redirects users to a healthy one.

## What is S3?
Amazon S3 (Simple Storage Service) is an object storage service on AWS used to store and retrieve any amount of data — like images, videos, backups, logs, or even static websites — from anywhere on the internet.

We use S3 because it’s highly durable (99.999999999%), scalable, and cost-effective and integrates easily with many AWS services — for example, CloudFront for faster delivery, Lambda for automation, or CloudWatch for monitoring.

**What if availability zones, s3 bucket or region goes down?**  
Data is always safe because AWS follows S3 replication mechanism.

**Versioning**  
If versioning is disabled, and you tried to upload a file 2nd time with updated content, then the old old file will get uploaded and replaces the old one. But if versioning is enabled, then the different version of same file can be seen in version tab of same file.

## What is AWS CLI?
AWS CLI, or Command Line Interface, is a tool that lets you control and manage AWS services directly from your terminal using simple commands instead of relying on the AWS Console.

When you install and configure AWS CLI, you provide your access key, secret key, and region. After that, whenever you type a command — like listing S3 buckets or starting an EC2 instance — the CLI securely sends an API request to AWS in the background. AWS processes that request and returns the result directly in your terminal. This makes it much easier for developers and DevOps engineers to interact with AWS, especially when building automation scripts or integrating AWS into CI/CD pipelines.

## What is AWS CFT ?
AWS CloudFormation (CFT) is a service that helps you automate the process of creating and managing AWS infrastructure using code instead of doing it manually through the console. Normally, when you build something on AWS — like EC2 instances, VPCs, load balancers, or S3 buckets — you’d click around in the dashboard to set everything up. But with CloudFormation, you describe all those resources in a single YAML or JSON file called a template.

Once the template is ready, CloudFormation reads it and automatically provisions everything exactly as defined. This approach is called Infrastructure as Code (IaC) — meaning your infrastructure setup is stored as code, so it’s repeatable, version-controlled, and consistent across environments.

*For example, if your team needs the same setup for multiple regions, you can simply deploy the same CloudFormation template — AWS will create all the resources automatically.*

## What is AWS CLOUD FRONT?
AWS CloudFront is a **Content Delivery Network (CDN)** service that speeds up the delivery of your website’s content — like images, videos, HTML, CSS, or APIs — to users all around the world. Normally, if your website is hosted in one region (say, Mumbai) and someone tries to access it from London, it takes longer because data has to travel across continents. CloudFront solves this problem by storing copies of your content in **edge locations** — data centers located in multiple cities globally — so that users always get data from the nearest location instead of the main server.

The main purpose of CloudFront is to **reduce latency and improve performance**. When a user requests your website, CloudFront checks if the content is already available in the nearest edge location (called a cache). If it is, it delivers it instantly; if not, it fetches it from the origin (like an S3 bucket or EC2 instance), delivers it to the user, and stores a copy for future requests.

It’s also used for **security and reliability**. CloudFront integrates with services like AWS Shield and AWS WAF to protect against DDoS attacks and block malicious traffic before it reaches your server.

In short, AWS CloudFront acts as a global layer in front of your application that makes your website faster, safer, and more reliable by delivering content efficiently from the nearest AWS edge location.

## What is Lambda function?
AWS Lambda is a **serverless compute service** that lets you run your code without managing servers. Instead of setting up and maintaining EC2 instances, you just upload your function code to Lambda, and AWS automatically takes care of everything — from provisioning servers to scaling and running the code whenever it’s needed.

The reason Lambda is used is because it’s **cost-efficient and highly scalable**. You only pay for the exact time your code runs — measured in milliseconds — not for idle server time. This makes it ideal for event-driven tasks like processing S3 uploads, responding to API Gateway requests, or automating system tasks.

When an event occurs (for example, a file is uploaded to S3 or an HTTP request hits an API endpoint), AWS triggers your Lambda function automatically. The function executes the logic you’ve written — like resizing an image, validating data, or updating a database — and then shuts down. There’s no need to manually start or stop servers.

In simple terms, **Lambda lets developers focus purely on writing logic, not managing infrastructure**. It’s the backbone of serverless architecture on AWS, making applications faster to build, easier to scale, and cheaper to run.

##  What is AWS Config?
**AWS Config** is a service that helps you continuously monitor, record, and evaluate the configuration of your AWS resources. It basically acts like a *watchdog* that keeps track of how your AWS environment changes over time — what resources you have, their settings, and whether they meet your organization’s compliance or security rules.

The main reason AWS Config is important is **visibility and compliance**. In large environments, it’s hard to manually track every EC2 instance, S3 bucket, or security group setting. Config helps by automatically recording any changes, such as if someone opens a port in a security group or makes an S3 bucket public. You can then compare these changes against predefined rules to check if your infrastructure follows best practices or company policies.

*Here’s how it works:* once AWS Config is enabled, it starts recording configuration details of your resources (like VPCs, EC2, IAM roles, etc.) and stores this data in an S3 bucket. You can then create **Config Rules** — for example, “S3 buckets must not be public” — and AWS Config will continuously evaluate your setup to see if it complies. If something violates a rule, it can send an alert or even trigger an automated fix through AWS Lambda.

In short, **AWS Config helps you maintain control, security, and compliance** by giving you a clear picture of what’s happening in your AWS account and automatically ensuring your resources stay within policy.




## What is AWS Elastic Container Registry (ECR)?
AWS ECR is a fully managed service where we store our container images. It works just like Docker Hub, but it’s more secure and tightly integrated with AWS services like ECS, EKS, and CodePipeline. Developers push images to ECR and Kubernetes or ECS can pull those images automatically during deployment.

## What is Elastic Container Service (ECS)?
AWS ECS (Elastic Container Service) is a fully managed service that runs and scales Docker containers. It takes care of scheduling, running, and monitoring containers, so developers don’t have to manage servers manually. It can run on EC2 or in a serverless way using AWS Fargate.

## What is AWS Elastic Kubernetes Service (EKS)?
AWS EKS is a managed Kubernetes service by Amazon. It helps you run Kubernetes clusters without having to install or manage the Kubernetes control plane (the master nodes). You just focus on your applications (pods, deployments, services), and AWS handles the heavy lifting — like control plane setup, security, and scaling.

## ECS vs EKS vs Kubernetes