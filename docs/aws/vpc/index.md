---
title: VPC
icon: material/lan
---

## Introduction to AWS VPC

An **Amazon Virtual Private Cloud (VPC)** is a logically isolated section of the AWS Cloud where you can launch AWS resources in a virtual network that you define. You have complete control over your virtual networking environment, including selection of your own IP address range, creation of subnets, and configuration of route tables and network gateways.

---

## Subnetting in AWS VPC

A **subnet** is a range of IP addresses in your VPC. Subnets allow you to segment your VPC's network, typically by Availability Zone (AZ) or by function (public/private). Each subnet must reside entirely within one AZ.

- **Public Subnet:** Connected to the internet via an Internet Gateway.
- **Private Subnet:** No direct internet access; can access the internet via a NAT Gateway.

**Subnetting Example:**

If your VPC CIDR is `10.0.0.0/16`, you can create:
- `10.0.1.0/24` (256 addresses) for public subnet in AZ-a
- `10.0.2.0/24` (256 addresses) for private subnet in AZ-a
- `10.0.3.0/24` (256 addresses) for public subnet in AZ-b
- `10.0.4.0/24` (256 addresses) for private subnet in AZ-b

---

## IP Map Cheat Sheet

| CIDR Block      | # of Addresses | Typical Use Case                |
|-----------------|:--------------:|---------------------------------|
| /32             | 1              | Single host (e.g., loopback)    |
| /30             | 4              | Point-to-point links            |
| /29             | 8              | Small subnets, NAT Gateway      |
| /28             | 16             | Small public/private subnets    |
| /24             | 256            | Standard subnet size            |
| /16             | 65,536         | Large VPC, many subnets         |

> **Note:** AWS reserves 5 IPs per subnet.

---

## Availability Zones (AZs)

An **Availability Zone** is a physically isolated data center within an AWS Region. Distributing resources across multiple AZs increases fault tolerance and availability. When you create subnets, you specify the AZ to ensure redundancy.

---

## Internet Gateways

An **Internet Gateway (IGW)** is a horizontally scaled, redundant, and highly available VPC component that allows communication between instances in your VPC and the internet. Attach an IGW to your VPC and update the route table for your public subnets to direct internet-bound traffic to the IGW.

---


## NAT Gateways

A **NAT Gateway** enables instances in a private subnet to connect to the internet or other AWS services, but prevents the internet from initiating connections with those instances. Deploy NAT Gateways in public subnets and update private subnet route tables to direct outbound traffic to the NAT Gateway.

---

## VPC Endpoints

An **AWS VPC Endpoint** enables you to privately connect your VPC to supported AWS services and VPC endpoint services powered by PrivateLink, without requiring an internet gateway, NAT device, VPN connection, or AWS Direct Connect. Instances in your VPC do not require public IP addresses to communicate with resources in the service.

### Why Use VPC Endpoints?

- **Enhanced Security:** Traffic between your VPC and AWS services stays within the AWS network, never traversing the public internet.
- **No Need for NAT/IGW:** Access AWS services from private subnets without a NAT Gateway or Internet Gateway.
- **Granular Access Control:** Use endpoint policies to control which resources and actions are allowed through the endpoint.

### Types of VPC Endpoints

1. **Interface Endpoints**
	- An elastic network interface (ENI) with a private IP address from your VPC.
	- Used for most AWS services (e.g., S3, DynamoDB, SSM, Secrets Manager, etc.).
	- Supports PrivateLink for connecting to services across VPCs/accounts.

2. **Gateway Endpoints**
	- A gateway that you specify as a target for a route in your route table.
	- Only available for S3 and DynamoDB.

### Example: Gateway Endpoint for S3

1. **Create the Endpoint:**
	- Go to the VPC console → Endpoints → Create Endpoint.
	- Select service: `com.amazonaws.<region>.s3`
	- Type: Gateway
	- Attach to route tables for your private subnets.

2. **Route Table Update:**
	- Adds a route for S3 traffic to the endpoint, so private subnet resources can access S3 without public IPs or NAT.

**Sample Route Table Entry:**

| Destination     | Target         |
|-----------------|---------------|
| pl-xxxxxxxx     | S3 Endpoint   |

### Example: Interface Endpoint for SSM

1. **Create the Endpoint:**
	- Go to the VPC console → Endpoints → Create Endpoint.
	- Select service: `com.amazonaws.<region>.ssm`
	- Type: Interface
	- Select subnets and security groups for the ENI.

2. **DNS Integration:**
	- AWS automatically creates private DNS names for the service, so your applications can use the standard AWS service endpoints.

**Sample Security Group Rule:**

| Type        | Protocol | Port | Source        |
|-------------|----------|------|--------------|
| HTTPS       | TCP      | 443  | VPC CIDR     |

### Configuration Options

- **Policy:** Attach an endpoint policy to control access to the service via the endpoint.
- **Private DNS:** Enable to use AWS service DNS names that resolve to the endpoint.
- **Security Groups:** For interface endpoints, control which resources can connect to the endpoint ENI.

### Use Cases

- Access S3 from private subnets without NAT Gateway
- Connect to AWS services (e.g., SSM, Secrets Manager) securely from private networks
- Enable hybrid architectures with on-premises connectivity

> **Tip:** Use VPC Endpoints to reduce data transfer costs and improve security posture by keeping traffic within AWS.

---

## Example: Secure, Fault-Tolerant VPC Design

**Architecture:**
- VPC CIDR: `10.0.0.0/16`
- 2 Public Subnets (one per AZ): `10.0.1.0/24`, `10.0.3.0/24`
- 2 Private Subnets (one per AZ): `10.0.2.0/24`, `10.0.4.0/24`
- 1 Internet Gateway attached to VPC
- 2 NAT Gateways (one per AZ, in public subnets)
- Application servers in private subnets
- Load balancer in public subnets

**Key Points:**
- Public subnets host NAT Gateways and Load Balancers.
- Private subnets host application/database servers.
- Route tables ensure only public subnets have direct internet access.
- NAT Gateways provide outbound internet for private subnets.
- Resources are distributed across AZs for high availability.

**Diagram:**

```
+----------------------------- VPC (10.0.0.0/16) -----------------------------+
|                                                                             |
|  +-----------+           +-----------+           +-----------+              |
|  | Public    |           | Private   |           | Public    |              |
|  | Subnet A  |           | Subnet A  |           | Subnet B  |              |
|  | 10.0.1.0/24|          | 10.0.2.0/24|          | 10.0.3.0/24|             |
|  |  [NAT GW] |           | [App/DB]  |           | [NAT GW]  |              |
|  |  [LB]     |           |           |           | [LB]      |              |
|  +-----------+           +-----------+           +-----------+              |
|         |                      |                        |                   |
|         +----------------------+------------------------+                   |
|                                |                                            |
|                        [Internet Gateway]                                  |
+-----------------------------------------------------------------------------+
```

---

## Summary

AWS VPC provides a flexible, secure, and scalable networking foundation. By understanding subnetting, AZs, and gateway components, you can design robust architectures that are both secure and highly available.