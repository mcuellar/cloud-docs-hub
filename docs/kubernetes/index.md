---
title: Kubernetes
icon: material/kubernetes
---

# Kubernetes: A Comprehensive Guide

## What is Kubernetes?

**Kubernetes** (often abbreviated as K8s) is an open-source platform designed to automate deploying, scaling, and operating containerized applications. Originally developed by Google, it is now maintained by the Cloud Native Computing Foundation (CNCF).

Kubernetes provides a robust framework for running distributed systems resiliently, handling scaling and failover for your applications, and providing deployment patterns and more.

<div class="mdx-admonition mdx-admonition-type-info">
<p class="mdx-admonition-title">Did you know?</p>
Kubernetes is named after the Greek word for "helmsman" or "pilot." The abbreviation K8s comes from counting the eight letters between the "K" and the "s".
</div>

---

## Pros and Cons

### Pros

- **Scalability:** Easily scale applications up or down automatically or manually.
- **High Availability:** Built-in mechanisms for self-healing, replication, and load balancing.
- **Portability:** Run workloads on-premises, in the cloud, or in hybrid environments.
- **Declarative Configuration:** Infrastructure as code using YAML manifests.
- **Ecosystem:** Large, active community and rich ecosystem of tools and extensions.

### Cons

- **Complexity:** Steep learning curve and operational overhead.
- **Resource Intensive:** Requires significant compute and memory resources for control plane components.
- **Debugging:** Troubleshooting distributed systems can be challenging.
- **Security:** Misconfigurations can lead to vulnerabilities; requires careful RBAC and network policy management.

---

## Limitations

- **Stateful Workloads:** While supported, running stateful applications can be more complex than stateless ones.
- **Networking:** Advanced networking (multi-cluster, cross-cloud) can be difficult to set up.
- **Persistent Storage:** Requires integration with external storage providers.
- **Operational Overhead:** Upgrades, monitoring, and maintenance require expertise.

---

## Use Cases

- **Microservices Architectures**
- **CI/CD Pipelines**
- **Batch Processing and Data Pipelines**
- **Hybrid and Multi-Cloud Deployments**
- **Self-Healing Applications**

---

## Dependencies

- **Container Runtime:** (e.g., containerd, CRI-O, Docker)
- **etcd:** Distributed key-value store for cluster state.
- **Networking Plugin:** (e.g., Calico, Flannel, Cilium)
- **Cloud Provider Integrations:** For storage, load balancers, etc.

---

## Basic Kubernetes Configuration Sample

### Deployment YAML

<span class="md-typeset__small">A <strong>Deployment YAML</strong> defines how to deploy and manage a set of identical pods (containers) in Kubernetes. It specifies the desired state, such as the number of replicas, the container image, and update strategy. Deployments ensure your application is running and can self-heal if pods fail.</span>
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
	name: my-app
spec:
	replicas: 3
	selector:
		matchLabels:
			app: my-app
	template:
		metadata:
			labels:
				app: my-app
		spec:
			containers:
			- name: my-app
				image: nginx:latest
				ports:
				- containerPort: 80
```

### Service YAML

<span class="md-typeset__small">A <strong>Service YAML</strong> defines a stable network endpoint to expose your application running in pods. It enables communication within the cluster or externally, and can load-balance traffic to multiple pod replicas. The <code>type: LoadBalancer</code> exposes the service externally using a cloud provider's load balancer.</span>
```yaml
apiVersion: v1
kind: Service
metadata:
	name: my-app-service
spec:
	selector:
		app: my-app
	ports:
		- protocol: TCP
			port: 80
			targetPort: 80
	type: LoadBalancer
```

### Quick Start with Minikube
```bash
# Install Minikube and kubectl, then start a local cluster
minikube start
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
minikube service my-app-service
```

---

## Comparison: Kubernetes vs. AWS EC2 Deployment

| Feature                | Kubernetes                                 | AWS EC2 (Manual)                  |
|------------------------|--------------------------------------------|-----------------------------------|
| **Scalability**        | Auto-scaling, rolling updates              | Manual scaling, scripting needed  |
| **High Availability**  | Built-in, self-healing                     | Manual setup (ELB, ASG)           |
| **Deployment Speed**   | Fast, declarative, automated               | Slower, manual or scripted        |
| **Resource Utilization** | Efficient bin-packing, shared nodes      | Dedicated VMs, less efficient     |
| **Cost**               | Can be lower with right-sizing             | May be higher, less dense         |
| **Learning Curve**     | Steep                                     | Moderate                          |
| **Management**         | Automated, declarative                     | Manual, imperative                |

### Example: Deploying a Web App

#### On Kubernetes
```yaml
# See deployment and service YAML above
```

#### On AWS EC2 (User Data Script)
```bash
#!/bin/bash
sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo service docker start
sudo usermod -a -G docker ec2-user
docker run -d -p 80:80 nginx:latest
```

---

## Further Reading

- [Kubernetes Official Documentation](https://kubernetes.io/docs/)
- [CNCF Kubernetes Landscape](https://landscape.cncf.io/category=platform)
- [AWS EKS (Managed Kubernetes)](https://aws.amazon.com/eks/)

<details>
<summary>Show More: Useful kubectl Commands</summary>

```bash
# Get all pods in all namespaces
kubectl get pods --all-namespaces

# Describe a deployment
kubectl describe deployment my-app

# View cluster nodes
kubectl get nodes
```

</details>
