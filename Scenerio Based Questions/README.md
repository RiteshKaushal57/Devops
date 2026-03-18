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
4. I would also **evaluate network latency and whether caching is being used effectively**, because frequent direct hits to the database can overload it. Based on what I find, I would **optimize queries, add proper indexes, introduce or improve caching, or scale the database resources**, so that performance improves and the system can handle the load efficiently. 🚀

