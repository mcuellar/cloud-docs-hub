## AWS & Kubernetes Interview Questions and Answers

### What is the difference between Security Group and NACL?
- **Security Group**: Acts as a virtual firewall at the instance level. It is stateful (return traffic is automatically allowed) and only supports allow rules.
- **NACL (Network ACL)**: Operates at the subnet level. It is stateless (return traffic must be explicitly allowed) and supports both allow and deny rules.

---

### How to recover an EC2 instance if the key pair is lost?
- Stop the instance (do not terminate).
- Detach the root EBS volume and attach it to another instance.
- Modify the authorized_keys file to add a new key.
- Reattach the volume to the original instance and start it.

---

### VPC Peering vs Transit Gateway?
- **VPC Peering**: Direct connection between two VPCs. No transitive routing; must create peering for each pair.
- **Transit Gateway**: Central hub for connecting multiple VPCs and on-premises networks. Supports transitive routing and simplifies large network architectures.

---

### How to troubleshoot an EC2 server?
- Check instance status and system logs.
- Verify security group and NACL rules.
- Check CPU, memory, and disk metrics (CloudWatch).
- Review application and OS logs.
- Test network connectivity (ping, telnet, traceroute).

---

### In how many ways can we connect to a private instance inside a VPC?
- Bastion Host (jump box)
- VPN connection
- AWS Systems Manager Session Manager
- VPC Peering (if source is in another VPC)
- Transit Gateway

---

### What is Kubernetes architecture?
- Master (Control Plane): API Server, Controller Manager, Scheduler, etcd.
- Worker Nodes: Kubelet, Kube Proxy, container runtime.
- Pods run on worker nodes; control plane manages cluster state.

---

### Deployment vs StatefulSet?
- **Deployment**: For stateless applications; pods are interchangeable.
- **StatefulSet**: For stateful applications; pods have stable identities and persistent storage.

---

### Explain your project infrastructure?
*Sample answer, customize as needed:*
- Multi-AZ VPC with public/private subnets.
- Load balancer in public subnet.
- Application servers in private subnets.
- RDS database in private subnet.
- NAT Gateway for outbound internet.
- Security groups and IAM roles for access control.

---

### Write a Terraform script to create an EC2 instance?
```hcl
provider "aws" {
	region = "us-east-1"
}

resource "aws_instance" "example" {
	ami           = "ami-0c55b159cbfafe1f0"
	instance_type = "t2.micro"
	tags = {
		Name = "ExampleInstance"
	}
}
```

---

### What would happen if the state file is deleted?
- Terraform loses track of managed resources.
- Cannot update or destroy existing infrastructure safely.
- Must import resources or recreate the state file.

---

### How can you set up VPC, subnet, and route table for a gaming application and leaderboard with a database for minimum latency?
- Place application and database in the same region and AZ if possible.
- Use private subnets for app and DB, public subnet for load balancer.
- Route table directs internet traffic via IGW for public subnet, NAT for private.
- Use placement groups for low-latency networking.

---

### High Availability and DR strategies?
- Multi-AZ deployment for redundancy.
- Automated backups and cross-region replication.
- Use Auto Scaling Groups and Elastic Load Balancer.
- Regular DR drills and failover testing.

---

### How would you manage billing for Cloud during high traffic load?
- Set up AWS Budgets and CloudWatch billing alarms.
- Use cost allocation tags.
- Enable Cost Explorer and analyze usage.
- Use Savings Plans or Reserved Instances for predictable workloads.

---

### What is Kubernetes Architecture?
*See above for details.*

---

### Difference between Pod, Node, and Cluster?
- **Pod**: Smallest deployable unit; runs one or more containers.
- **Node**: Worker machine (VM or physical) that runs pods.
- **Cluster**: Set of nodes managed by the control plane.

---

### Horizontal Pod Autoscaler vs Vertical Pod Autoscaler?
- **Horizontal**: Scales number of pod replicas based on metrics (CPU, memory).
- **Vertical**: Adjusts resource requests/limits for individual pods.

---

### If Developers say there is a latency issue, how would you reduce the latency to Kubernetes pods?
- Check pod resource limits and node utilization.
- Use node affinity/anti-affinity for optimal placement.
- Enable cluster autoscaling.
- Optimize network policies and service routing.
- Use local persistent storage if needed.

---

### If a node goes down, what happens to the pod?
- Kubernetes automatically reschedules the pod to another healthy node.

---

### What would be your action for it?
- Investigate node failure (logs, metrics).
- Ensure enough resources for rescheduling.
- Replace unhealthy nodes if needed.

---

### What are ConfigMaps and Secrets?
- **ConfigMap**: Stores non-sensitive configuration data as key-value pairs.
- **Secret**: Stores sensitive data (passwords, tokens) in base64-encoded form; access is restricted.

---
