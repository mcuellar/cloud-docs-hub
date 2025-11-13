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

### What is an Auto Scaling Group?
An **Auto Scaling Group (ASG)** in AWS is a service that automatically manages the number of EC2 instances in a group based on demand, health checks, and scaling policies. It helps ensure high availability and cost efficiency by scaling resources up or down as needed.

**Key Features:**
- Automatic scaling based on metrics (CPU, memory, custom CloudWatch alarms)
- Health checks and automatic replacement of unhealthy instances
- Integration with Elastic Load Balancer
- Multi-AZ deployments for high availability
- Scheduled scaling actions

**Configuration Example (Terraform):**
```hcl
resource "aws_launch_template" "web" {
	name_prefix   = "web-"
	image_id      = "ami-0c55b159cbfafe1f0"
	instance_type = "t2.micro"
}

resource "aws_autoscaling_group" "web_asg" {
	name                      = "web-asg"
	min_size                  = 2
	max_size                  = 5
	desired_capacity          = 3
	vpc_zone_identifier       = ["subnet-12345", "subnet-67890"]
	launch_template {
		id      = aws_launch_template.web.id
		version = "$Latest"
	}
	target_group_arns         = [aws_lb_target_group.app_tg.arn]
	health_check_type         = "EC2"
	health_check_grace_period = 300
	tags = [
		{
			key                 = "Name"
			value               = "WebASGInstance"
			propagate_at_launch = true
		}
	]
}
```

**Summary Table**

| Feature                | Description                                      |
|------------------------|--------------------------------------------------|
| Scaling Policies       | Scale in/out based on metrics or schedules       |
| Health Checks          | Replace unhealthy instances automatically        |
| Multi-AZ Support       | Distribute instances across multiple AZs         |
| Integration            | Works with ELB, CloudWatch, EC2                  |
| Cost Optimization      | Runs only required number of instances           |


### What is Route53?
### What is Route53?

**AWS Route 53** is a scalable and highly available Domain Name System (DNS) web service. It is used to route end-user requests to applications hosted in AWS or on-premises, and provides domain registration, DNS management, and health checking.

**Key Features:**
- Domain registration and management
- DNS record management (A, AAAA, CNAME, MX, TXT, etc.)
- Health checks and DNS-based failover
- Traffic routing policies (Simple, Weighted, Latency, Failover, Geolocation, Geoproximity)
- Integration with AWS services (CloudFront, S3, ELB)
- Private hosted zones for VPCs

**Configuration Example (Terraform):**
```hcl
resource "aws_route53_zone" "main" {
	name = "example.com"
}

resource "aws_route53_record" "web" {
	zone_id = aws_route53_zone.main.zone_id
	name    = "www"
	type    = "A"
	ttl     = 300
	records = ["1.2.3.4"]
}
```

**Failover Scenario Example:**
Route 53 can automatically route traffic to a healthy endpoint if the primary fails.

```hcl
resource "aws_route53_record" "primary" {
	zone_id = aws_route53_zone.main.zone_id
	name    = "app"
	type    = "A"
	set_identifier = "primary"
	failover_routing_policy {
		type = "PRIMARY"
	}
	health_check_id = aws_route53_health_check.primary.id
	ttl     = 60
	records = ["1.2.3.4"]
}

resource "aws_route53_record" "secondary" {
	zone_id = aws_route53_zone.main.zone_id
	name    = "app"
	type    = "A"
	set_identifier = "secondary"
	failover_routing_policy {
		type = "SECONDARY"
	}
	health_check_id = aws_route53_health_check.secondary.id
	ttl     = 60
	records = ["5.6.7.8"]
}

resource "aws_route53_health_check" "primary" {
	type = "HTTP"
	resource_path = "/health"
	fqdn = "app.example.com"
	port = 80
}

resource "aws_route53_health_check" "secondary" {
	type = "HTTP"
	resource_path = "/health"
	fqdn = "app.example.com"
	port = 80
}
```

**Summary Table**

| Feature                | Description                                   |
|------------------------|-----------------------------------------------|
| Domain Registration    | Register and manage domain names              |
| DNS Management         | Create and manage DNS records                 |
| Health Checks          | Monitor endpoint health for failover          |
| Traffic Routing        | Multiple routing policies for global apps     |
| Private Hosted Zones   | DNS for internal AWS resources                |
| Integration            | Works with ELB, S3, CloudFront, etc.         |

### What are the different types of AWS Load Balancers and what are their differences?

AWS offers four main types of load balancers:

- **Application Load Balancer (ALB)**: Operates at Layer 7 (HTTP/HTTPS). Supports advanced routing (host-based, path-based), WebSocket, and is ideal for microservices and containerized applications (ECS/EKS).
- **Network Load Balancer (NLB)**: Operates at Layer 4 (TCP/UDP). Handles millions of requests per second, provides ultra-low latency, and supports static IP addresses. Suitable for high-performance, real-time applications.
- **Classic Load Balancer (CLB)**: Legacy option supporting both Layer 4 and Layer 7. Provides basic load balancing features. Use ALB or NLB for new applications.
- **Gateway Load Balancer (GWLB)**: Operates at Layer 3. Used to deploy, scale, and manage third-party virtual appliances (e.g., firewalls, intrusion detection systems) transparently.

#### Example Configurations

**Application Load Balancer (ALB) - Terraform Example**
```hcl
resource "aws_lb" "app_lb" {
	name               = "app-lb"
	internal           = false
	load_balancer_type = "application"
	subnets            = ["subnet-12345", "subnet-67890"]
	security_groups    = ["sg-123456"]
}

resource "aws_lb_listener" "app_lb_listener" {
	load_balancer_arn = aws_lb.app_lb.arn
	port              = "80"
	protocol          = "HTTP"
	default_action {
		type             = "forward"
		target_group_arn = aws_lb_target_group.app_tg.arn
	}
}
```

**Network Load Balancer (NLB) - Terraform Example**
```hcl
resource "aws_lb" "net_lb" {
	name               = "net-lb"
	internal           = false
	load_balancer_type = "network"
	subnets            = ["subnet-12345", "subnet-67890"]
}

resource "aws_lb_listener" "net_lb_listener" {
	load_balancer_arn = aws_lb.net_lb.arn
	port              = "80"
	protocol          = "TCP"
	default_action {
		type             = "forward"
		target_group_arn = aws_lb_target_group.net_tg.arn
	}
}
```

**Classic Load Balancer (CLB) - Terraform Example**
```hcl
resource "aws_elb" "classic_lb" {
	name               = "classic-lb"
	subnets            = ["subnet-12345", "subnet-67890"]
	security_groups    = ["sg-123456"]

	listener {
		instance_port     = 80
		instance_protocol = "http"
		lb_port           = 80
		lb_protocol       = "http"
	}
}
```

**Gateway Load Balancer (GWLB) - Terraform Example**
```hcl
resource "aws_lb" "gwlb" {
	name               = "gwlb"
	load_balancer_type = "gateway"
	subnets            = ["subnet-12345", "subnet-67890"]
}
```

**Summary Table**

| Load Balancer Type | OSI Layer | Use Case                              | Key Features                        |
|--------------------|-----------|---------------------------------------|-------------------------------------|
| ALB                | 7         | Web, microservices, containers        | Advanced routing, HTTP/HTTPS        |
| NLB                | 4         | High-performance, real-time           | TCP/UDP, static IP, low latency     |
| CLB                | 4 & 7     | Legacy applications                   | Basic load balancing                |
| GWLB               | 3         | Security appliances integration       | Transparent, scales appliances      |

---

### OSI Layer Descriptions
The OSI (Open Systems Interconnection) model is a conceptual framework that standardizes the functions of a networking or telecommunication system into seven distinct layers. It helps guide product developers and facilitates interoperability between different systems and protocols by defining how data should be transmitted and received across a network.

| Layer | Name             | Description                                               |
|-------|------------------|----------------------------------------------------------|
| 7     | Application      | End-user applications and network services (HTTP, SMTP)  |
| 6     | Presentation     | Data translation, encryption, and compression            |
| 5     | Session          | Establishes, manages, and terminates sessions            |
| 4     | Transport        | Reliable data transfer, error recovery (TCP/UDP)         |
| 3     | Network          | Routing, addressing, and packet forwarding (IP)          |
| 2     | Data Link        | Node-to-node data transfer, MAC addressing               |
| 1     | Physical         | Transmission of raw bits over physical medium            |

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
