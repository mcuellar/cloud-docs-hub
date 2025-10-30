# AWS EC2 Comprehensive Guide

Amazon Elastic Compute Cloud (EC2) provides scalable computing capacity in the AWS cloud. It enables users to run virtual servers, known as instances, to host applications securely and efficiently.

---

## EC2 Instance Types

EC2 offers a variety of instance types optimized for different use cases:

| Instance Type | vCPU | Memory (GiB) | Typical Use Case                | On-Demand Price (us-east-1) |
|---------------|------|--------------|---------------------------------|------------------------------|
| t3.micro      | 2    | 1            | Low-traffic web servers, dev    | $0.0104/hr                   |
| m6i.large     | 2    | 8            | General purpose, small DBs      | $0.096/hr                    |
| c6g.xlarge    | 4    | 8            | Compute-intensive workloads     | $0.136/hr                    |
| r6i.2xlarge   | 8    | 64           | Memory-intensive applications   | $0.512/hr                    |
| p4d.24xlarge  | 96   | 1152         | Machine learning, HPC           | $32.77/hr                    |

> **Note:** Prices are subject to change. Refer to the [AWS Pricing Calculator](https://calculator.aws.amazon.com/) for up-to-date pricing.

---

## Common Use Cases

- **Web Hosting:** t3 and m6i instances for scalable web applications.
- **Big Data Analytics:** r6i and c6g for memory and compute-intensive processing.
- **Machine Learning:** p4d for GPU-accelerated workloads.
- **Development & Testing:** t3.micro for cost-effective environments.

---

## Configuration Options

### Instance Configuration

- **AMI (Amazon Machine Image):** Pre-configured OS and software.
- **Instance Type:** Choose based on CPU, memory, storage, and networking needs.
- **Key Pair:** For SSH access.
- **Network & Subnet:** Placement within a VPC.
- **IAM Role:** Assign permissions to instances.
- **Storage:** EBS volumes (SSD/HDD), instance store.

### Security

- **Security Groups:** Virtual firewalls to control inbound/outbound traffic.
- **NACLs:** Additional subnet-level security.
- **Encryption:** EBS and data-in-transit encryption.

### Monitoring & Management

- **CloudWatch:** Monitor metrics, set alarms.
- **Auto Scaling:** Automatically adjust capacity.
- **Elastic Load Balancer:** Distribute traffic across instances.

---

## User Data

User data allows you to automate instance configuration at launch using shell scripts or cloud-init directives.

**Example (Linux):**
```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "Hello from EC2" > /var/www/html/index.html
```
- Add this script in the "User data" field during instance launch.
- Used for bootstrapping, installing software, or configuring settings.

---

## Other

- **Placement Groups:** For low-latency or high-throughput workloads.
- **Spot Instances:** Cost savings for flexible, interruption-tolerant tasks.
- **Elastic IPs:** Static public IP addresses for dynamic cloud computing.
- **Instance Metadata:** Retrieve instance-specific data from within the instance.
- **Termination Protection:** Prevent accidental instance termination.
- **Reserved Instances & Savings Plans:** Optimize long-term costs.

---

## References

- [EC2 Documentation](https://docs.aws.amazon.com/ec2/)
- [EC2 Pricing](https://aws.amazon.com/ec2/pricing/)
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
