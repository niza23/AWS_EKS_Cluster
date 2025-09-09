# 📘 Amazon Elastic Kubernetes Service (EKS)

## 1. What is EKS?
Amazon Elastic Kubernetes Service (EKS) is a **managed Kubernetes service by AWS**.  
Instead of installing and managing Kubernetes on your own servers or EC2 instances, AWS manages the **control plane** for you.  
- You still manage **worker nodes (EC2 or Fargate)**.  
- AWS ensures the control plane is **highly available, secure, and automatically updated**.  

👉 Think of it like:  
- **Self-managed Kubernetes** = You build and maintain the entire car.  
- **EKS** = AWS gives you the engine (control plane) fully maintained, you just decide the seats (nodes) and where to drive (apps).  

---

## 2. Why do we need EKS?
Kubernetes is powerful but **complex to manage**. With self-managed Kubernetes, you must handle:  
- Setting up and upgrading API servers, etcd, controller managers  
- Ensuring high availability (multi-AZ setup)  
- Security patching and monitoring  

EKS solves this by:  
✅ Offloading control plane management to AWS  
✅ Seamlessly integrating with AWS ecosystem (IAM, VPC, CloudWatch, ELB, ECR, etc.)  
✅ Making scaling easier (both for workloads and control plane)  

👉 In short: You get **Kubernetes power** without **control plane headaches**.  

---

## 3. How does EKS work? (Functionality)

### 🧠 AWS Managed Control Plane
- **API Server**: Entry point for all Kubernetes commands (`kubectl`, AWS CLI).  
- **etcd**: Stores cluster state (what pods, deployments exist).  
- **Controller Manager & Scheduler**: Ensure desired state (e.g., if a pod dies, another is created).  
- AWS keeps this HA across multiple AZs.  

### 💪 Worker Nodes (your responsibility)
- EC2 instances or Fargate tasks that run your Pods (containers).  
- Components on each node:  
  - **kubelet**: Talks to control plane.  
  - **kube-proxy**: Manages networking.  
  - **Container runtime**: Runs containers (Docker, containerd).  

### 🌐 Networking
- Runs inside your AWS VPC.  
- Load Balancers (ELB/ALB/NLB) expose services.  
- Security groups and IAM roles enforce security.  

### 👩‍💻 Users
- Developers interact via `kubectl` or AWS CLI.  
- Users access apps via LoadBalancer or Ingress.  

---

## 3.1 EKS Functioning Diagram

```
                     Amazon EKS - High Level Architecture
                     ====================================

                    ┌─────────────────────────────────┐
                    │   AWS Managed Control Plane      │
                    │---------------------------------│
                    │  • API Server (Kubernetes API)  │
                    │  • etcd (Cluster state storage) │
                    │  • Controller Manager           │
                    │  • Scheduler                    │
                    │  (Highly Available & Scalable)  │
                    └─────────────────────────────────┘
                                   │
                                   │ (You connect via kubectl / AWS CLI)
                                   ▼
                    ┌─────────────────────────────────┐
                    │   Worker Nodes (EC2 instances)  │
                    │---------------------------------│
                    │  • Kubelet (agent on each node) │
                    │  • kube-proxy (networking)      │
                    │  • Container Runtime (Docker/CRI│
                    │  • Pods (containers grouped)    │
                    └─────────────────────────────────┘
                                   │
                     Schedules Pods │
                                   ▼
                    ┌─────────────────────────────────┐
                    │     Networking & Security       │
                    │---------------------------------│
                    │  • VPC (isolated network)       │
                    │  • Subnets (public/private)     │
                    │  • Security Groups (firewalls)  │
                    │  • Load Balancers (ELB/ALB/NLB) │
                    └─────────────────────────────────┘
                                   │
                      Expose Apps  │
                                   ▼
                    ┌─────────────────────────────────┐
                    │   End Users / Applications      │
                    │---------------------------------│
                    │  • Access apps via LoadBalancer │
                    │  • Or via Ingress (domain/HTTP) │
                    └─────────────────────────────────┘
```

---

## 4. Benefits of EKS
✅ **Managed control plane** → AWS handles complexity  
✅ **Scalable** → grows with your workloads  
✅ **Secure & compliant** → integrates with IAM & AWS security standards  
✅ **Ecosystem ready** → works with AWS services (CloudWatch, ECR, ELB, etc.)  
✅ **Community-driven** → pure Kubernetes, compatible with open-source tooling  

---

## 5. Trade-offs
⚠️ **Cost** → EKS charges per cluster + worker nodes  
⚠️ **Less control** → You can’t tweak the control plane deeply  
⚠️ **Learning curve remains** → You still need Kubernetes knowledge (pods, services, YAMLs, etc.)  

---

📌 **Summary**:  
EKS is AWS’s way of giving you Kubernetes **without the hardest parts**.  
You still manage worker nodes and workloads, but AWS ensures the control plane is always healthy, scalable, and secure.  

