## 1. What is Observability?
Observability is the ability to understand what is happening inside a system by analyzing its outputs like `logs`, `metrics`, and `traces`. It is important because in complex distributed systems, it helps us quickly identify and troubleshoot issues instead of guessing. It works by collecting different types of data — 
1. `Monitoring (Metrics):` involves tracking system metrics like CPU usage, memory usage, and network performance. *Monitoring tells us what is happening.*    
2. `Logging (Logs):` involves the collection of log data from various components of a system. *Logging explains why it is happening.*    
3. `Tracing(Traces):` involves tracking the flow of a request or transaction as it moves through different services and components within a system. *Tracing shows how it is happening*.

## 2. What is the Difference Between Monitoring and Observability?
Monitoring and observability are related but not the same; monitoring is about tracking predefined metrics and setting alerts for known issues, while observability is about understanding the overall system behavior, including unknown problems. Monitoring is important because it helps you detect when something goes wrong based on thresholds like high CPU or low memory, but its limitation is that it only tells you that something is wrong, not why it happened. Observability goes a step further by using logs, metrics, and traces together to give deeper insights into the system, allowing you to investigate and find the root cause even for unexpected issues. In practice, monitoring works by setting up dashboards and alerts for known conditions, whereas observability works by collecting rich data and enabling you to explore and debug complex systems dynamically.

## 3. What is Metrics?
Metrics are numerical data that show the performance and health of a system, such as CPU usage or response time. They are important because they help us quickly detect issues and monitor system behavior. They work by collecting data at regular intervals and using dashboards and alerts to track trends and identify problems.
