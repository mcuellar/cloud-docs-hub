## AWS Architecture Interview Questions and Answers

### 1. What is a multi-AZ deployment in AWS and why is it important?

**Answer:**  
A multi-AZ (Availability Zone) deployment distributes resources across multiple physically separated data centers within a region. It improves fault tolerance, high availability, and disaster recovery by ensuring that if one AZ fails, the application can continue running in another.

---

### 2. How would you design a scalable web application on AWS?

**Answer:**  
Use Elastic Load Balancer (ELB) to distribute traffic across multiple EC2 instances in an Auto Scaling Group, deploy instances across multiple AZs, store static assets in S3, use RDS/Aurora for databases with Multi-AZ enabled, and leverage CloudFront for global content delivery.

---

### 3. What is the difference between a public and a private subnet in a VPC?

**Answer:**  
A public subnet has a route to the internet via an Internet Gateway, allowing resources to be accessed from the internet. A private subnet does not have a direct route to the internet; resources in private subnets typically access the internet via a NAT Gateway or NAT instance.

---

### 4. How do you secure data at rest and in transit in AWS?

**Answer:**  
- At rest: Use encryption services like AWS KMS, enable encryption for S3, EBS, RDS, and DynamoDB.
- In transit: Use SSL/TLS for data transfer, enforce HTTPS for APIs and websites, and use VPC peering or VPN for private connections.

---

### 5. What is an AWS Auto Scaling Group and how does it work?

**Answer:**  
An Auto Scaling Group automatically adjusts the number of EC2 instances based on demand, using scaling policies and CloudWatch alarms. It helps maintain application availability and optimize costs by scaling out during high load and scaling in when demand drops.

---

### 6. How would you implement high availability for a database in AWS?

**Answer:**  
Use Amazon RDS or Aurora with Multi-AZ deployment, enable automated backups and snapshots, use read replicas for scaling reads, and consider cross-region replication for disaster recovery.

---

### 7. What is AWS CloudFront and how does it improve application performance?

**Answer:**  
CloudFront is a Content Delivery Network (CDN) that caches content at edge locations worldwide, reducing latency and improving load times for users by serving content from the nearest location.

---

### 8. How do you design a serverless architecture on AWS?

**Answer:**  
Use AWS Lambda for compute, API Gateway for RESTful APIs, S3 for storage, DynamoDB for NoSQL databases, and EventBridge or SNS/SQS for event-driven workflows. Serverless architectures reduce operational overhead and scale automatically.

---

### 9. What is the purpose of AWS Route 53?

**Answer:**  
Route 53 is a scalable Domain Name System (DNS) web service that routes end-user requests to AWS resources or external endpoints. It supports health checks, routing policies (e.g., latency-based, weighted), and domain registration.

---

### 10. How would you implement disaster recovery for an AWS workload?

**Answer:**  
Implement regular backups and snapshots, use Multi-AZ and cross-region replication, automate failover with Route 53, and define a disaster recovery plan (pilot light, warm standby, or multi-site active-active) based on RTO/RPO requirements.

---
