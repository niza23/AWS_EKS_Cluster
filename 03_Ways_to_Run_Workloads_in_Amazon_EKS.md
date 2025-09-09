

# 📘 Different Ways to Run Workloads in Amazon EKS

When you create an EKS cluster, AWS manages the **control plane** (API server, etcd, scheduler).
But you need to decide **how your workloads (pods) will run**.
There are **3 main ways**:

---

## 1. **Managed Node Groups (AWS-managed EC2)**

* AWS creates and manages an **Auto Scaling Group** of EC2 instances for you.
* AWS keeps the EC2 nodes patched and updated (you decide when to upgrade).
* You just choose instance type, number of nodes, scaling rules.

✅ Easy setup and maintenance
⚠️ You still pay for EC2 (always on, even if no pods are running)

### Text Diagram

```
   ┌───────────────────────────────┐
   │       EKS Control Plane        │  (Managed by AWS)
   └──────────────┬────────────────┘
                  │
       ┌──────────┴──────────┐
       │ Managed Node Group   │  (AWS creates EC2 instances)
       │  • Auto Scaling      │
       │  • EC2 lifecycle     │
       └──────────┬──────────┘
                  │
             Your workloads (Pods)
```

---

## 2. **Self-Managed Nodes (DIY EC2)**

* You launch your own EC2 instances and connect them to the EKS cluster.
* You install the EKS-optimized AMI or build a custom AMI.
* You are responsible for updates, scaling, patching, and node replacement.

✅ Maximum flexibility (choose OS, AMI, Spot/Reserved mix)
⚠️ More work: you manage upgrades, HA, and lifecycle

### Text Diagram

```
   ┌───────────────────────────────┐
   │       EKS Control Plane        │  (Managed by AWS)
   └──────────────┬────────────────┘
                  │
       ┌──────────┴──────────┐
       │ Self-Managed Nodes   │  (You create & configure EC2 instances)
       │  • Custom AMIs       │
       │  • Manual scaling    │
       │  • Your responsibility│
       └──────────┬──────────┘
                  │
             Your workloads (Pods)
```

---

## 3. **AWS Fargate (Serverless Pods)**

* No EC2 at all – AWS runs your pods directly on its own infrastructure.
* Each pod gets isolated compute, auto-provisioned on demand.
* You only pay for CPU & memory requested by the pod.

✅ Zero infra management, very secure, pay-as-you-go
⚠️ Limited features (no GPUs, no DaemonSets, not good for large workloads)

### Text Diagram

```
   ┌───────────────────────────────┐
   │       EKS Control Plane        │  (Managed by AWS)
   └──────────────┬────────────────┘
                  │
       ┌──────────┴──────────┐
       │     AWS Fargate      │  (Serverless: no EC2 nodes)
       │  • Pods run directly │
       │  • No servers to manage │
       └──────────┬──────────┘
                  │
             Your workloads (Pods)
```

---

## 📊 Comparison Table

| Feature       | Managed Node Groups       | Self-Managed Nodes          | AWS Fargate              |
| ------------- | ------------------------- | --------------------------- | ------------------------ |
| Servers       | AWS-managed EC2           | You manage EC2              | No servers (serverless)  |
| Customization | Medium                    | High (custom AMIs, configs) | Low                      |
| Scaling       | AWS Auto Scaling Groups   | You configure scaling       | Automatic by AWS         |
| Cost Model    | Pay for EC2               | Pay for EC2 (Spot/RI mix)   | Pay per pod CPU & memory |
| Best For      | Standard apps, easy setup | Advanced/custom workloads   | Small, serverless apps   |

---

📌 **Summary**:

* **Managed Node Groups** → Easy EC2 option (AWS manages lifecycle).
* **Self-Managed Nodes** → DIY EC2 (flexible but more work).
* **Fargate** → No servers, just pods (simple but limited).

---


