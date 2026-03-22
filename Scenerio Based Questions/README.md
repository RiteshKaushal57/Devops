### 1. Production website is down. What are your first 5 steps?
1. If the production website goes down, the first thing I would do is **confirm whether the outage is real and affecting users**, so I would check the website from multiple locations and also verify monitoring alerts to see if the issue is global or limited to a specific region.    
2. Once the outage is confirmed, I would **check the load balancer health** to understand whether it is receiving traffic and whether the backend servers are passing or failing health checks, because if targets are unhealthy the load balancer cannot route requests properly.    
3. After that, I would **verify the compute layer**, such as EC2 instances or Kubernetes pods, to make sure the servers are running normally and not crashing or restarting repeatedly.   
4. Then I would **check recent changes in the system**, like deployments, configuration updates, scaling actions, or security rule changes, since many production outages happen due to recent modifications.   
5. Finally, I would **analyze application logs and monitoring metrics** to identify errors or unusual behavior, while also keeping the team and stakeholders informed so they know the impact and the progress toward resolving the issue.

### 2. EC2 is running but the website is not accessible. How do you debug? 
1. If the EC2 instance is running but the website is not accessible, I would first **check the security group rules** to make sure inbound traffic on ports 80 or 443 is allowed from the required sources, because if these ports are blocked users cannot reach the website.        
2. If the security group looks correct, I would **verify the network configuration**, such as the subnet route table, to confirm that traffic can reach the internet through an Internet Gateway or a load balancer.    
3. After confirming the network path, I would **log into the EC2 instance and check whether the web server service like Nginx or Apache is running and listening on the correct port**, since sometimes the server might be down even though the instance is running.       
4. Then I would **review the web server and application logs** to see if there are errors causing the service to fail and also check whether the instance is passing health checks if it is behind a load balancer.       
5.- Finally, I would **inspect other possible network issues such as NACL rules, DNS configuration, or SSL certificate problems**, because these can also prevent users from accessing the website even when the server itself is running.

### 3. CPU utilization suddenly spikes to 95%. What will you do?
1. If CPU utilization suddenly spikes to 95%, the first thing I would do is **check monitoring metrics to understand whether the spike is temporary or continuously increasing**, because sometimes CPU increases briefly due to traffic bursts or scheduled jobs.    
2. If it looks sustained, I would **identify which process or application component is consuming the CPU** by checking instance or container-level monitoring tools, since that helps pinpoint whether the issue is coming from the application, background jobs, or some unexpected process.    
3. If the high CPU usage is caused by **increased user traffic**, I would allow **Auto Scaling or horizontal scaling** to add more instances or pods so the load gets distributed.   
4. If the spike is caused by a **bug, memory leak, or inefficient code**, I would take immediate mitigation steps like restarting the service and then review logs and recent deployments to find what triggered the issue.    
5. After stabilizing the system, I would **analyze the incident and adjust scaling policies, instance size, or resource limits** so the system can handle similar load in the future without causing performance problems.

### 4. Traffic increases 5x unexpectedly. What happens in your architecture? 
If traffic suddenly increases 5x, in a well-designed architecture the system should handle it automatically without manual intervention.   
1. The **load balancer will first distribute the incoming traffic across all available healthy servers**, so that no single instance gets overloaded.   
2. As the load increases, **Auto Scaling groups or Kubernetes scaling mechanisms will detect the higher CPU, memory, or request rate and automatically launch additional instances or pods** to handle the demand.    
3. At the same time, **caching layers like Redis or CDN services such as CloudFront help reduce pressure on backend services** by serving frequently requested content faster.   
4. For the database layer, **read replicas or scaling mechanisms handle the increased read traffic**, while monitoring and alerting systems notify the team so they can observe system behavior and take action if anything unusual happens.

*NOTE: 4th point simply means that when traffic increases, a lot more users start requesting data from the database, like loading products, profiles, dashboards, etc. If all these requests go to one single database, it can become slow or overloaded. So to avoid this, we create read replicas, which are copies of the main database that only handle read requests (like SELECT queries). The main database handles writes (insert, update, delete), while the replicas share the load by answering read requests, which helps the system stay fast even when traffic increases.*

*At the same time, monitoring and alerting systems like CloudWatch, Prometheus, or Datadog keep watching metrics such as CPU usage, database connections, query latency, or error rates. If something unusual happens, like the database becoming slow or connections reaching the limit, the system sends alerts to the team so they can quickly investigate and take action before it impacts users.*

### 5. How would you design a highly available web application? 
1. I would deploy the application across **multiple Availability Zones**, so if one zone fails the application continues running in another.   
2. All incoming traffic would first go through a **load balancer**, which distributes requests across healthy servers and performs health checks to avoid sending traffic to failed instances.   
3. The **application layer would be stateless**, so new servers can be added or removed easily using **Auto Scaling** when traffic changes.   
4. For the data layer, I would use a **managed database with Multi-AZ replication**, so if the primary database fails a standby replica can take over automatically.   
5. Static files such as images, CSS, and JavaScript would be stored in **S3 and delivered through a CDN** to improve performance and reduce load on application servers.   
6. Finally, I would implement **monitoring, alerting, automated backups, and tested failover procedures** so the team can quickly detect issues and recover the system while keeping the application highly available for users.

### 6. How would you migrate an on-prem application to cloud?
1. To migrate an on-prem application to the cloud, I would first **understand the existing application setup**, including its architecture, dependencies, data size, and performance needs, because this helps decide the right migration approach.   
2. Based on that, I would choose a strategy like **lift-and-shift if I need quick migration**, or re-platforming if I want some optimization during the move.   
3. Then I would **set up the required cloud infrastructure in advance**, such as servers, networking, and storage, so everything is ready before moving the application.   
4. After that, I would **migrate the data using secure and reliable methods** and deploy the application in a staging environment to test whether everything is working as expected.   
5. Once I am confident, I would **plan a controlled cutover**, either phased or with minimal downtime, along with a rollback plan in case something goes wrong.   
6. Finally, after the migration is complete, I would **optimize the application for scalability, security, and cost in the cloud environment** to fully utilize cloud benefits.

### 7. Database is slow. How do you identify the bottleneck? 
1. If the database becomes slow, I would first **check key performance metrics like CPU usage, memory, disk I/O, and number of connections** to understand if the issue is due to resource limits, because high usage in any of these can directly impact performance.     
2. Once I have that view, I would **analyze slow query logs to identify long-running or inefficient queries**, since poorly written queries are one of the most common reasons for database slowness.    
3. Then I would **look deeper into possible causes like missing indexes, table locking, or sudden increase in traffic**, as well as check if the application is making too many unnecessary database calls.   
4. I would also **evaluate network latency and whether caching is being used effectively**, because frequent direct hits to the database can overload it. Based on what I find, I would **optimize queries, add proper indexes, introduce or improve caching, or scale the database resources**, so that performance improves and the system can handle the load efficiently. 

### 8. Users complain about slow performance. What will you check?
1. If users report slow performance, I would first **validate their complaint by checking monitoring and metrics to see when and where latency is increasing**, because this helps confirm whether the issue is real and identify affected regions or time periods.   
2. Then I would **break down the system layer by layer**, starting with frontend performance, backend response time, and database metrics, to figure out which part is causing the delay.    
3. After identifying the affected layer, I would **check logs for errors, timeouts, or unusual patterns**, and also verify whether any third-party services or APIs are slowing things down.    
4. At the same time, I would **review recent deployments or configuration changes**, since performance issues often come after updates. This step-by-step approach helps quickly isolate the root cause and take the right action to improve performance. 

### 9. Kubernetes pod is in CrashLoopBackOff. Steps to debug? 
1. First, I would **describe the pod to check events and restart reasons**, because Kubernetes usually shows why the container is failing, like probe failures or crash exit codes.

2. Then, I would **check the container logs to understand the actual error**, since logs clearly indicate if the issue is due to application crash, wrong configuration, or missing dependencies.

3. Next, I would **verify environment variables, secrets, and mounted volumes**, because incorrect or missing configuration is a common reason for pods failing during startup.

4. After that, I would **check resource limits like CPU and memory**, to see if the pod is getting terminated due to OOM (out of memory) or insufficient resources.

5. Finally, if the issue is still unclear, I would **try to reproduce it in a staging or test environment**, so I can debug safely and fix the problem before applying it in production.

### 10. How do you implement zero downtime deployment? 
1. First, I would **use a deployment strategy like rolling, blue-green, or canary**, where the new version is introduced gradually while the old version is still running, so users don’t experience downtime.

2. Then, I would **use a load balancer with health checks**, to ensure traffic is only sent to healthy instances and not to servers that are still starting or failing.

3. Next, I would **deploy the new version step by step**, replacing old instances one by one or shifting small traffic to the new version, so if something breaks the impact is minimal.

4. I would also **handle database changes carefully by keeping them backward compatible**, because both old and new versions may run at the same time during deployment.

5. Finally, I would **keep rollback or pause options ready**, so if any issue is detected, I can quickly switch back to the stable version and ensure users don’t face any disruption.

### 11. How would you reduce cloud cost in a running project?
1. First, I would **analyze the cost reports to understand where most of the money is being spent**, because this helps identify which services are contributing the highest cost.

2. Then, I would **look for underutilized or unused resources**, such as oversized EC2 instances, unused EBS volumes, idle load balancers, or unattached Elastic IPs, since these waste money without adding value.

3. Next, I would **right-size the compute resources**, either by downsizing instances or using auto scaling, so the infrastructure matches actual demand instead of running at full capacity all the time.

4. I would also **optimize storage by moving less frequently used data to cheaper storage tiers and deleting unnecessary snapshots**, which helps reduce long-term storage costs.

5. Finally, I would **use Reserved Instances or Savings Plans for long-running workloads and ensure non-production environments are turned off when not needed**, so overall cloud spending becomes more efficient without affecting performance. 

### 12. Private EC2 cannot connect to RDS. How will you troubleshoot?
1. First, I would **check whether the EC2 instance and RDS are in the same VPC or properly peered VPCs**, because without network connectivity between them, communication is not possible.

2. Then, I would **verify the RDS security group inbound rules**, to ensure it allows traffic on the database port (like 3306 or 5432) from the EC2 instance’s security group, since this is the most common issue.

3. Next, I would **check the EC2 security group outbound rules**, to confirm it allows traffic to the RDS database port, because blocked outbound rules can also prevent connection.

4. After that, I would **validate the database endpoint, port, username, and password**, to make sure the connection details are correct and not causing authentication failures.

5. Finally, if everything looks correct, I would **check NACL rules, route tables, and DNS resolution**, to ensure there are no network-level restrictions stopping traffic inside the VPC. 

### 13. How do you design disaster recovery for production? 
1. First, I would **understand business requirements like RTO (how quickly we need to recover) and RPO (how much data loss is acceptable)**, because these define the level of disaster recovery needed.

2. Then, I would **identify critical components such as databases, application servers, and external dependencies**, so I know what must be protected and recovered first.

3. Next, I would **ensure data is regularly backed up and, if required, replicated to another region**, so even if one region fails, data is still safe and available.

4. Based on the criticality, I would **choose the right DR strategy like backup & restore, pilot light, warm standby, or active-active**, so recovery can happen within the required time.

5. Finally, I would **use Infrastructure as Code to recreate environments quickly and perform regular DR drills**, so we can validate that recovery actually works and meet the expected timelines during a real failure. 

### 14. A new deployment caused errors in production. What will you do?
1. First, I would **check the impact by looking at monitoring dashboards, error rates, and user reports**, so I understand how many users are affected and how critical the issue is.

2. If the issue is severe, I would **immediately stop the deployment or roll back to the previous stable version**, because restoring service quickly is the top priority.

3. Then, I would **analyze logs, metrics, and recent code or deployment changes**, to identify what exactly caused the error in production.

4. Next, I would **verify if the issue is related to configuration, database changes, or compatibility issues**, since these are common causes after deployments.

5. Finally, after fixing the issue, I would **redeploy carefully and introduce safeguards like canary deployments, better testing, or monitoring alerts**, so similar problems can be avoided in the future. 

### 15. How would you secure a fintech/payment application architecture? 
1. First, I would **follow a defense-in-depth approach**, where multiple layers of security are applied, so even if one layer fails the system is still protected.

2. Then, I would **secure the network by using private subnets, strict security groups, and limited entry points**, so only required traffic can access the system.

3. Next, I would **implement IAM with least privilege and strong authentication like MFA**, to ensure users and services only have the minimum access they need.

4. I would also **encrypt sensitive data both at rest and in transit using secure key management services**, because financial data must always be protected.

5. After that, I would **store secrets securely and rotate them regularly**, so credentials are not exposed or misused over time.

6. Finally, I would **enable logging, monitoring, and alerts, ensure compliance like PCI-DSS, and perform regular security audits and testing**, so any threats are detected early and the system stays secure continuously. 

### 16. A private EC2 cannot access the internet. How will you debug? 
1. First, I would **check the private subnet’s route table**, to ensure it has a default route (0.0.0.0/0) pointing to a NAT Gateway or NAT instance, because private instances need NAT to access the internet.

2. Then, I would **verify that the NAT Gateway is placed in a public subnet and that subnet has a route to the Internet Gateway**, since without this path, internet access will not work.

3. Next, I would **check the EC2 security group outbound rules**, to confirm that traffic to the internet is allowed, as blocked outbound rules can stop connectivity.

4. After that, I would **review NACL rules for both inbound and outbound traffic**, to ensure they are not restricting the required ports or responses.

5. I would also **confirm that DNS resolution is working properly**, because even if networking is correct, failed DNS can make it look like there is no internet access.

6. Finally, I would **test connectivity from the instance (like ping or curl)** to identify whether the issue is with routing, security configuration, or application-level problems.

### 17. Users cannot access your public website. What layers will you check? 
1. First, I would **check the DNS configuration**, to ensure the domain is resolving to the correct IP or load balancer, because if DNS is wrong users cannot reach the system at all.

2. Then, I would **verify whether the load balancer is reachable and has healthy targets**, since it is responsible for routing traffic to backend servers.

3. Next, I would **check security groups and NACLs**, to confirm that inbound traffic on required ports (like 80/443) is allowed and not blocked at the network level.

4. After that, I would **verify that the application is running properly on backend instances**, to ensure servers are up and responding to requests.

5. Finally, I would **analyze logs and monitoring metrics across all layers**, so I can identify exactly where the failure is happening and take the correct action. 

### 18. Traffic is reaching Load Balancer but not EC2. What could be wrong?
1. First, I would **check whether the EC2 instances are healthy in the target group**, because if they are marked unhealthy, the load balancer will not forward traffic to them.

2. Then, I would **verify the EC2 security group inbound rules**, to ensure it allows traffic from the load balancer’s security group on the correct port, since blocked rules can stop requests from reaching the instance.

3. Next, I would **confirm that the application is running and listening on the expected port**, because even if the instance is up, traffic won’t work if the app is not accepting connections.

4. After that, I would **check the health check configuration**, including path, port, and response, since incorrect health checks can mark instances as unhealthy even when they are working.

5. Finally, I would **review logs and configuration for any mismatch in ports or routing**, because issues like wrong target port or misconfiguration are common reasons why traffic reaches the load balancer but not the EC2 instances. 

### 19. EC2 can ping internally but cannot connect to database. Why? 
1. First, I would **understand that ping working means basic network connectivity is fine**, so the issue is likely related to ports or permissions, not routing.

2. Then, I would **check the database security group inbound rules**, to ensure it allows traffic from the EC2 security group on the correct database port (like 3306 or 5432), because this is the most common cause.

3. Next, I would **verify the EC2 security group outbound rules**, to confirm it allows traffic to the database port, since blocked outbound rules can also prevent connection.

4. After that, I would **check NACL rules**, to ensure they are not blocking the required inbound or outbound traffic at the subnet level.

5. I would also **validate the database endpoint and port**, to make sure the application is trying to connect to the correct address.

6. Finally, I would **look for database-level issues like wrong credentials, authentication failure, or connection limits**, because even if networking is correct, these can still block access. 

### 20. You connected two VPCs but instances cannot communicate. What will you verify? 
1. First, I would **check whether the VPC peering or Transit Gateway connection is active**, because if the connection itself is not established, communication cannot happen at all.

2. Then, I would **verify route tables in both VPCs**, to ensure each VPC has a route to the other VPC’s CIDR block pointing to the peering connection, since without proper routing traffic won’t flow.

3. Next, I would **check security groups on both sides**, to confirm that inbound and outbound traffic is allowed between the instances, as blocked rules can prevent communication.

4. After that, I would **review NACL rules**, to ensure there are no subnet-level restrictions blocking the required ports or traffic.

5. Finally, I would **confirm that the CIDR blocks of both VPCs do not overlap**, because overlapping IP ranges break routing and prevent proper communication. 

### 21. Route tables look correct but traffic still not flowing. What next? 
1. First, I would **move beyond routing and check security groups**, to ensure the required ports and protocols are allowed, because even with correct routes, blocked SG rules can stop traffic.

2. Then, I would **review NACL rules carefully**, checking both inbound and outbound rules, since NACLs are stateless and can silently block traffic in either direction.

3. Next, I would **verify DNS resolution**, to make sure the service is resolving to the correct IP address, because wrong DNS can look like a connectivity issue.

4. After that, I would **test connectivity from the instance using tools like ping, telnet, or curl**, to identify whether the issue is network-level or application-level.

5. Finally, I would **analyze VPC flow logs and monitoring data**, to see exactly where traffic is being dropped, which helps pinpoint the root cause quickly. 

### 22. Application slows down during peak hours. How will you investigate? 
1. First, I would **correlate the slowdown with traffic patterns**, to confirm whether the issue happens during peak hours, because that indicates a load-related problem.

2. Then, I would **analyze metrics across all layers like load balancer latency, application response time, CPU, memory, and database performance**, to identify which layer is becoming the bottleneck.

3. Next, I would **check whether Auto Scaling or pod scaling is triggering properly**, and verify if the newly added instances are actually handling traffic, because scaling issues can cause slowdowns during high load.

4. After that, I would **review database performance and slow queries**, to see if the database is unable to handle increased requests and causing delays.

5. Finally, I would **check caching efficiency, connection limits, and third-party services**, because poor caching or slow external dependencies can also degrade performance under heavy traffic. 

### 23. Auto Scaling is configured but instances are not scaling. Why? 
1. First, I would **check whether scaling policies are correctly configured and attached to the Auto Scaling Group**, because if policies are missing or misconfigured, scaling will not trigger at all.

2. Then, I would **verify the metrics like CPU, memory, or request count**, to see if they are actually crossing the defined thresholds, since scaling only happens when conditions are met.

3. Next, I would **review cooldown periods and min/max limits**, because long cooldowns or restrictive limits can prevent new instances from launching.

4. After that, I would **check permissions and capacity issues**, ensuring the Auto Scaling Group has the right IAM role and there are no problems like instance type unavailability or subnet IP exhaustion.

5. Finally, I would **analyze Auto Scaling activity logs**, to identify any failed scaling attempts or errors, which helps understand exactly why scaling is not happening. 

### 24. CPU is low but response time is high. What might be the issue? 
1. First, I would **understand that low CPU but high response time usually means the bottleneck is not compute**, so scaling instances may not fix the issue.

2. Then, I would **check database performance**, including latency, slow queries, and connection pool limits, because the application might be waiting on the database.

3. Next, I would **look at network-related factors like load balancer latency, request queues, or delays in external API calls**, since waiting on network responses can increase response time.

4. After that, I would **analyze application-level issues such as thread pool limits, locks, or blocking operations**, because the system might be idle in CPU but waiting internally.

5. I would also **check caching efficiency and I/O operations**, since missing cache or slow disk/network I/O can cause delays.

6. Finally, I would **focus on application profiling instead of just infrastructure scaling**, to identify exactly where time is being spent and fix the root cause. 

### 25. One Availability Zone goes down. What should happen in a properly designed system? 
1. First, the system should **automatically detect that the Availability Zone is unhealthy**, and the load balancer should stop sending traffic to instances in that AZ, so users are not routed to failed resources.

2. Then, **traffic should be redirected to healthy instances in other AZs**, ensuring the application remains accessible without manual intervention.

3. Next, **Auto Scaling should launch new instances in the remaining healthy AZs**, to replace the lost capacity and handle the load.

4. For the database layer, **Multi-AZ or replicated databases should automatically fail over to a standby instance**, so data access continues with minimal disruption.

5. Finally, users might experience **slight latency or temporary degradation**, but the application should continue running because it is designed across multiple AZs with no single point of failure. 

### 26. Users from another country complain about latency. How do you reduce it? 
1. First, I would **analyze where the latency is happening by checking request paths and geographic traffic patterns**, because this helps identify whether the delay is due to distance, network, or backend processing.

2. Then, I would **use a CDN to cache content closer to users**, so static and even some dynamic content is served from nearby locations, reducing response time significantly.

3. Next, I would **deploy the application in multiple regions and use geo-based routing**, so users are directed to the nearest region instead of a far-away server.

4. After that, I would **optimize APIs by enabling compression and reducing payload size**, because smaller responses travel faster over the network.

5. Finally, I would **use database read replicas closer to users if needed**, so read requests don’t have to travel long distances, improving overall performance. 

### 27. Design a highly available 3-tier architecture. 
1. First, I would **design the architecture in three layers: presentation (web), application, and database**, so responsibilities are clearly separated and easier to scale and manage.

2. Then, I would **place a load balancer in front of the web layer across multiple Availability Zones**, so incoming traffic is distributed only to healthy servers and there is no single point of failure.

3. Next, I would **run the application layer in an Auto Scaling setup across multiple AZs**, keeping it stateless so instances can be added or removed easily based on traffic.

4. For the database layer, I would **use a managed database with Multi-AZ replication or clustering**, so if one instance fails, another can take over automatically.

5. I would also **store static content in object storage and serve it via a CDN**, to reduce load on application servers and improve performance.

6. Finally, I would **implement monitoring, alerting, backups, and automated recovery across all layers**, so the system remains highly available and can recover quickly from failures. 

### 28. How would you eliminate single points of failure in a setup? 
1. First, I would **identify all components that exist as a single instance**, like one server, database, or NAT Gateway, because these are potential single points of failure.

2. Then, I would **replicate or distribute these components across multiple Availability Zones**, so if one fails, others can continue handling the workload.

3. Next, I would **use load balancers to distribute traffic across multiple instances**, ensuring that no single server is responsible for all requests.

4. I would also **enable Auto Scaling to automatically replace failed instances and handle traffic changes**, so the system can recover without manual intervention.

5. For the database layer, I would **use replication and automatic failover mechanisms**, so data remains available even if the primary database fails.

6. Finally, I would **ensure supporting components like DNS, networking, and authentication systems are highly available and monitored**, so the entire system remains resilient without hidden failure points. 

### 29. How would you design a system that can handle sudden traffic spikes? 
1. First, I would **design the application to be stateless**, so it can scale horizontally by adding more instances without worrying about session data.

2. Then, I would **configure Auto Scaling or container-based scaling to respond quickly to traffic spikes**, using metrics like CPU, memory, or request count, so capacity increases automatically.

3. Next, I would **use a load balancer to distribute traffic evenly across instances**, preventing any single server from getting overloaded.

4. I would also **introduce caching layers (like Redis or CDN)** to handle repeated requests, reducing load on backend services during spikes.

5. After that, I would **implement rate limiting and throttling**, to protect the system from sudden excessive traffic or abuse.

6. For the database, I would **use read replicas or partitioning**, so it can handle increased load without becoming a bottleneck.

7. Finally, I would **set up monitoring and alerting**, so the team is aware of sudden spikes and can take action if needed. 

### 30. How would you design backup and restore strategy for critical data? 
1. First, I would **classify the data based on its criticality and define RPO and RTO**, because this decides how frequently backups are needed and how quickly data must be restored.

2. Then, I would **automate backups and ensure they are encrypted**, so data is protected and consistently backed up without manual effort.

3. Next, I would **store backups in multiple locations or regions**, to protect against regional failures and ensure data availability.

4. For databases, I would **enable point-in-time recovery along with regular full snapshots**, so data can be restored to a specific moment if needed.

5. After that, I would **regularly test backups by restoring them in a non-production environment**, to make sure they actually work during real incidents.

6. Finally, I would **document the restore process and restrict access to backups with proper auditing**, so recovery is fast, secure, and reliable. 🚀

### 31. How would you ensure minimal downtime during maintenance? 
1. First, I would **use deployment strategies like rolling updates or blue-green deployments**, so maintenance happens gradually and the application remains available to users.

2. Then, I would **use a load balancer with health checks**, to ensure traffic is only sent to healthy instances and not to ones under maintenance.

3. Next, I would **validate new or updated instances before routing traffic to them**, so users are not affected by unstable or partially configured systems.

4. For the database, I would **use managed services with maintenance windows and automatic failover**, so updates happen with minimal impact on availability.

5. I would also **monitor the system closely during maintenance**, to quickly detect and fix any issues that arise.

6. Finally, I would **keep rollback plans ready**, so if anything goes wrong, I can quickly revert to a stable state and minimize downtime. 

### 32. How would you design secure access for internal employees? 
1. First, I would **use centralized identity management with SSO and MFA**, so all employees authenticate securely using a single system with strong verification.

2. Then, I would **follow least-privilege access by assigning roles instead of individual permissions**, so users only get access to what they actually need.

3. Next, I would **keep sensitive systems in private networks**, so they are not directly exposed to the internet and reduce the attack surface.

4. To access these systems, I would **use secure methods like VPN or identity-aware proxies**, ensuring only authorized employees can connect safely.

5. After that, I would **enable logging and auditing of all access**, so any activity can be tracked and monitored for security purposes.

6. Finally, I would **regularly review and update permissions**, to remove unnecessary access and prevent privilege creep over time. 

### 33. A developer accidentally made an S3 bucket public. What will you do? 
1. First, I would **immediately block public access at the bucket level**, to stop any further exposure of data and secure the system quickly.

2. Then, I would **review and fix bucket policies and ACLs**, to understand what was changed and revert it back to a secure configuration.

3. Next, I would **analyze access logs to assess the impact**, checking if any sensitive data was accessed or downloaded during the exposure.

4. After that, I would **rotate any exposed credentials and invalidate cached data if needed**, to prevent misuse of potentially compromised information.

5. Finally, I would **implement preventive measures like SCPs, automated checks, and alerts**, so similar incidents can be avoided in the future. 

### 34. How would you implement least privilege access across teams? 
1. First, I would **understand each team’s responsibilities and map the exact permissions they need**, so access is given only based on actual work requirements.

2. Then, I would **use IAM roles instead of assigning permissions directly to users**, so access is managed centrally and consistently across teams.

3. Next, I would **create fine-grained policies with specific services, actions, and resources**, avoiding wildcards or broad permissions to reduce security risks.

4. I would also **use permission boundaries where teams need flexibility**, so they can operate within controlled limits without getting full access.

5. For higher privileges, I would **provide temporary access instead of permanent permissions**, so sensitive access is limited in time and scope.

6. Finally, I would **perform regular audits and access reviews**, to remove unused permissions and prevent privilege creep over time. 

### 35. An EC2 was compromised. What immediate steps will you take? 
1. First, I would **isolate the EC2 instance immediately**, by removing it from the load balancer and restricting its network access, so the attack cannot spread to other systems.

2. Then, I would **preserve evidence by taking snapshots of the instance and attached volumes**, so forensic analysis can be done later to understand what happened.

3. Next, I would **rotate all credentials and revoke any compromised keys**, including IAM roles or access keys, to prevent further unauthorized access.

4. After that, I would **analyze logs and monitoring data**, to identify how the compromise occurred and check if any other systems are affected.

5. Finally, I would **terminate and rebuild the instance from a trusted, clean image**, instead of trying to fix the compromised one, to ensure the environment is fully secure again. 

### 36. How would you securely allow application to access database credentials? 
1. First, I would **store database credentials in a managed secrets service instead of hardcoding them**, so sensitive data is not exposed in code or configuration files.

2. Then, I would **allow the application to access secrets using IAM roles with least privilege**, so it can securely fetch credentials at runtime without storing them locally.

3. Next, I would **ensure credentials are encrypted both at rest and in transit**, to protect them from unauthorized access during storage and communication.

4. I would also **enable automatic credential rotation**, so passwords are updated regularly without manual intervention, reducing the risk of compromise.

5. After that, I would **design the application to handle credential rotation smoothly**, so it can continue working without downtime when credentials change.

6. Finally, I would **enable logging and monitoring for secret access**, to detect any unusual or unauthorized usage. 

### 37. How would you audit who accessed sensitive resources? 
1. First, I would **enable centralized logging and auditing across all services and accounts**, so every access event is captured in one place.

2. Then, I would **use audit logs to track who accessed which resource, when, and from where**, because this gives complete visibility into user activity.

3. Next, I would **store these logs in a secure and immutable location**, so they cannot be modified or deleted, ensuring reliable auditing.

4. After that, I would **use monitoring and querying tools to analyze access patterns**, to identify normal behavior and detect anomalies.

5. I would also **set up alerts for suspicious or unauthorized access attempts**, so the team can respond quickly to potential security issues.

6. Finally, I would **perform regular reviews and generate audit reports**, to meet compliance requirements and maintain strong security governance. 

### 38. Database storage is full. What are your options? 
1. First, I would **assess how critical the situation is**, checking whether the database is still writable or has become read-only, because that decides how urgent the fix needs to be.

2. Then, I would **take immediate actions like increasing storage size or freeing up space**, by deleting unused data, logs, or temporary files to quickly restore normal operation.

3. Next, I would **archive old or infrequently used data to cheaper storage**, so the database only holds active data and performance improves.

4. After that, I would **optimize data usage by cleaning logs, improving indexing, or using partitioning**, to manage storage more efficiently.

5. I would also **evaluate scaling options**, either vertically (bigger instance) or horizontally (sharding or read replicas), depending on the workload.

6. Finally, I would **set up monitoring and alerts on storage usage**, so the issue is detected early and does not happen again. 

### 39. How would you migrate data from one region to another safely? 
1. First, I would **assess data size, consistency requirements, and acceptable downtime**, because this helps decide the right migration approach and tools.

2. Then, I would **transfer data using secure and encrypted methods**, ensuring data is protected during migration and not exposed.

3. Next, I would **validate data integrity after transfer**, to confirm that no data is lost or corrupted before moving forward.

4. For databases, I would **use replication or snapshot-based migration**, so data stays in sync between source and target regions until the final switch.

5. After that, I would **test the migrated data in the target region**, to ensure applications work correctly with the new setup.

6. Finally, I would **perform a controlled cutover and monitor closely**, so any issues can be detected and resolved quickly during the transition. 

### 40. How would you handle backup failure alerts? 
1. First, I would **verify whether the alert is a real failure or just a temporary issue**, because sometimes backups fail due to transient network or service glitches.

2. Then, I would **identify which backup failed and check the reason**, such as permission issues, storage limits, or service errors, to understand the root cause.

3. Next, I would **immediately rerun the backup or trigger a manual backup**, so data protection is restored as quickly as possible.

4. After that, I would **fix the underlying issue causing the failure**, and test both backup and restore to ensure everything is working correctly.

5. Finally, I would **review and improve alerting, monitoring, and redundancy**, so future backup failures are detected early and handled proactively. 

