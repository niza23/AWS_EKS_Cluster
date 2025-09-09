
# 📘 Creating an EKS Cluster with Fargate (using `eksctl`)

### 🔹 Step 1: Create the Cluster

```bash
eksctl create cluster \
  --name my-fargate-cluster \
  --region us-east-1 \
  --fargate
```

✅ **What happens here**:

* Creates an **EKS control plane** (API server, etcd, controller manager).
* Creates the **networking (VPC, subnets, security groups)** for the cluster.
* Configures **IAM roles** so the cluster can manage resources.
* Adds **Fargate profile** for default namespaces (`default`, `kube-system`).

⚡ **Result**: You have a working EKS cluster, ready to run pods **without EC2 nodes**.

---

### 🔹 Step 2: Add a Fargate Profile (for your workloads)

By default, only system pods run on Fargate. To run your app in its own namespace:

```bash
eksctl create fargateprofile \
  --cluster my-fargate-cluster \
  --region us-east-1 \
  --name app-profile \
  --namespace my-app
```

✅ **What happens here**:

* Any pod deployed in the `my-app` namespace will run on **AWS Fargate**.
* You don’t need to worry about worker nodes, scaling, or patching.

---

### 🔹 Step 3: Verify the Cluster

Configure `kubectl` and check:

```bash
aws eks update-kubeconfig --region us-east-1 --name my-fargate-cluster
kubectl get nodes
```

👉 You won’t see EC2 nodes (since Fargate is serverless). Instead, your pods will run directly on AWS-managed infrastructure.

---

### 🔹 Step 4: Deploy a Sample App

```bash
kubectl create namespace my-app
kubectl run nginx --image=nginx --port=80 -n my-app
kubectl get pods -n my-app
```

✅ **Result**:
The Nginx pod will be scheduled to **Fargate**, without you managing any EC2 nodes.

---

⚡ **Summary**:

* `eksctl create cluster --fargate` → makes a serverless EKS cluster.
* `eksctl create fargateprofile` → decides which namespaces/apps run on Fargate.
* Pods run on AWS-managed infra (you don’t manage nodes).

---
---

# 📘 Creating an EKS Cluster with managed (using `eksctl`)

## 1. Why `eksctl` for Cluster Creation?

* Without `eksctl`, you would have to:

  * Manually create a VPC, subnets, and networking.
  * Set up IAM roles and node groups.
  * Use multiple AWS CLI commands.
* `eksctl` automates all of that with **a single command**.

---

## 2. Basic Cluster Creation Command

```bash
eksctl create cluster \
  --name my-cluster \
  --region us-east-1 \
  --nodes 2 \
  --node-type t3.medium \
  --managed
```

### 🔍 Explanation of Flags

* `--name my-cluster` → The name of your EKS cluster.
* `--region us-east-1` → AWS region where the cluster will be created.
* `--nodes 2` → Number of worker nodes in the cluster.
* `--node-type t3.medium` → EC2 instance type for worker nodes.
* `--managed` → Tells `eksctl` to use **managed node groups** (AWS handles scaling & upgrades for you).

**Result:**

* A new EKS cluster with 2 worker nodes in AWS.
* VPC, subnets, security groups, IAM roles automatically created.
* `kubectl` is automatically configured to talk to your new cluster.

---

## 3. Verify the Cluster

Once the cluster is created, check if your nodes are connected:

```bash
kubectl get nodes
```

You should see output like:

```
NAME                                           STATUS   ROLES    AGE   VERSION
ip-192-168-xx-xx.us-east-1.compute.internal   Ready    <none>   2m    v1.29.x
ip-192-168-yy-yy.us-east-1.compute.internal   Ready    <none>   2m    v1.29.x
```

This means your EKS cluster is up and running! 🎉

---

## 4. Clean Up (if you want to delete later)

```bash
eksctl delete cluster --name my-cluster --region us-east-1
```

This removes the cluster and all associated resources (nodes, networking, IAM roles, etc.).

---

⚡ In short:

* `eksctl create cluster` → makes the cluster in 1 shot.
* `kubectl get nodes` → confirms cluster is ready.
* `eksctl delete cluster` → removes it all.

---

