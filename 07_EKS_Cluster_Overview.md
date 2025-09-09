
## 1️⃣ EKS Cluster Overview

When you click on your cluster in **AWS Management Console → EKS → Clusters → Your Cluster**, you’ll see:

### **Basic Info**

* **Cluster name** – The name you gave your cluster.
* **Status** – Active means it’s running and ready.
* **Kubernetes version** – Version of K8s your cluster is using.
* **Endpoint** – The API server endpoint for `kubectl` to talk to your cluster.
* **Certificate authority data** – Used for TLS connections to your cluster.

---

## 2️⃣ Networking

Under **Networking** tab, you’ll see:

* **VPC ID** – The Virtual Private Cloud where your cluster lives.
* **Subnets** – Public & private subnets used for nodes/pods.
* **Security groups** – Controls which traffic can enter/leave your worker nodes.
* **Cluster endpoint access** – Usually `Public` or `Private` API server endpoint.
* **CIDR ranges** – IP ranges for pods & services.

💡 **Why important:**

* Ensures pods can communicate internally and with AWS services securely.

---

## 3️⃣ Compute (Nodes / Fargate Profiles)

* **Node groups** – If you used EC2 worker nodes, you’ll see them listed here.
* **Fargate profiles** – Since you’re using Fargate, you’ll see profiles specifying which pods (via namespace/labels) run on Fargate.
* **Scaling info** – How many nodes/pods can scale automatically.

💡 **Fargate profile example:**

* Namespace: `default`
* Labels: `app=frontend`
* This tells EKS: “Any pod in `default` namespace with `app=frontend` should run on Fargate.”

---

## 4️⃣ Add-ons

AWS provides managed add-ons, like:

* **CoreDNS** – DNS inside Kubernetes for service discovery.
* **kube-proxy** – Networking rules inside nodes/Fargate.
* **VPC CNI plugin** – Controls pod networking inside VPC.
* Optional: Amazon CloudWatch agent for logs and metrics.

💡 **Why important:**

* These are required for Kubernetes networking, service discovery, and observability.

---

## 5️⃣ Cluster Auto-Scaler / Metrics (if enabled)

* Scaling metrics for node groups or Fargate.
* Shows how pods scale based on CPU/memory (optional, depends on setup).

---

## 6️⃣ Namespaces & Workloads (via kubectl, not AWS UI)

* **AWS UI does not show Kubernetes namespaces** directly.
* To see namespaces and workloads, you use `kubectl`:

```bash
kubectl get namespaces
kubectl get pods --all-namespaces
kubectl get services --all-namespaces
```

* **Namespaces**: Logical partitioning inside Kubernetes (default, kube-system, etc.).
* **Pods/Deployments**: Actual workloads running inside your cluster.

---

## 7️⃣ Monitoring & Logging

If enabled, you can see:

* **CloudWatch Logs** – Logs from control plane or pods (if Fargate logging enabled).
* **CloudWatch Metrics** – Cluster CPU, memory, node/pod utilization.

---

## 8️⃣ Permissions & Security

* **IAM roles for service accounts (IRSA)** – Shows which Kubernetes service accounts are linked to IAM roles.
* **OIDC provider** – Shows the cluster’s OIDC endpoint.
* **Encryption settings** – If you enabled secrets encryption with KMS.

---

### 📊 Quick Text Diagram of EKS UI Structure

```
EKS Cluster
│
├─ Basic Info
│    ├─ Cluster Name
│    ├─ Status
│    ├─ Kubernetes Version
│    └─ Endpoint
│
├─ Networking
│    ├─ VPC ID
│    ├─ Subnets (public/private)
│    ├─ Security Groups
│    └─ Cluster Endpoint Access
│
├─ Compute
│    ├─ Node Groups (if EC2)
│    └─ Fargate Profiles
│         └─ Namespace / Labels mapping
│
├─ Add-ons
│    ├─ CoreDNS
│    ├─ kube-proxy
│    └─ VPC CNI Plugin
│
├─ Monitoring / Logging
│    ├─ CloudWatch Logs
│    └─ CloudWatch Metrics
│
└─ Permissions / Security
     ├─ IAM Roles for Service Accounts (IRSA)
     ├─ OIDC Provider
     └─ Encryption Settings
```

---

✅ **Summary**

* The AWS EKS UI gives you **cluster-level overview**, networking, compute profiles, add-ons, monitoring, and security info.
* **Namespaces, pods, and deployments** are **inside Kubernetes**, so you interact with them via `kubectl`.
* Fargate abstracts EC2 nodes — you only see **profiles and mappings**, not individual VMs.

---

