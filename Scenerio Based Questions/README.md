### 1. Production website is down. What are your first 5 steps?
- If the production website goes down, the first thing I would do is **confirm whether the outage is real and affecting users**, so I would check the website from multiple locations and also verify monitoring alerts to see if the issue is global or limited to a specific region.    
- Once the outage is confirmed, I would **check the load balancer health** to understand whether it is receiving traffic and whether the backend servers are passing or failing health checks, because if targets are unhealthy the load balancer cannot route requests properly.    
- After that, I would **verify the compute layer**, such as EC2 instances or Kubernetes pods, to make sure the servers are running normally and not crashing or restarting repeatedly.  
- Then I would **check recent changes in the system**, like deployments, configuration updates, scaling actions, or security rule changes, since many production outages happen due to recent modifications.   
- Finally, I would **analyze application logs and monitoring metrics** to identify errors or unusual behavior, while also keeping the team and stakeholders informed so they know the impact and the progress toward resolving the issue.

### 2. EC2 is running but the website is not accessible. How do you debug? 
- If the EC2 instance is running but the website is not accessible, I would first **check the security group rules** to make sure inbound traffic on ports 80 or 443 is allowed from the required sources, because if these ports are blocked users cannot reach the website.    
- If the security group looks correct, I would **verify the network configuration**, such as the subnet route table, to confirm that traffic can reach the internet through an Internet Gateway or a load balancer.    
- After confirming the network path, I would **log into the EC2 instance and check whether the web server service like Nginx or Apache is running and listening on the correct port**, since sometimes the server might be down even though the instance is running.    
- Then I would **review the web server and application logs** to see if there are errors causing the service to fail and also check whether the instance is passing health checks if it is behind a load balancer.    
- Finally, I would **inspect other possible network issues such as NACL rules, DNS configuration, or SSL certificate problems**, because these can also prevent users from accessing the website even when the server itself is running.
