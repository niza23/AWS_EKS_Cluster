

# 📘 Creating an EKS Cluster – 3 Approaches

## 1. **Using `eksctl` (Recommended, Fastest)**

```bash
eksctl create cluster \
  --name my-cluster \
  --region us-east-1 \
  --nodes 2 \
  --node-type t3.medium \
  --managed
```

🔍 **Explanation**

* `--name` → Cluster name
* `--region` → AWS region
* `--nodes` → Number of worker nodes
* `--node-type` → EC2 type for nodes
* `--managed` → Uses AWS managed node groups

✅ **Result**:

* EKS cluster created in minutes
* VPC, subnets, IAM roles auto-created
* `kubectl` is configured automatically

---

## 2. **Using AWS Management Console (UI)**

Steps:

1. Go to **AWS Console → EKS → Create Cluster**.
2. Fill in:

   * Cluster name
   * Kubernetes version
   * IAM role (EKS will assume this role)
   * VPC & subnets (use existing or let AWS create one)
3. Create the cluster → this sets up the **control plane**.
4. Next, create **Node Group**:

   * Define worker node instance type (e.g., `t3.medium`)
   * Number of nodes
   * Node IAM role
5. Launch → AWS provisions worker nodes and attaches them to the cluster.

✅ **Result**:

* EKS cluster + worker nodes ready.
* You’ll need to update your kubeconfig:

```bash
aws eks update-kubeconfig --region us-east-1 --name my-cluster
```

* Now you can run:

```bash
kubectl get nodes
```

---

## 3. **Using AWS CLI**

This method is more manual. You need to create each resource step by step.

### Step 1. Create the EKS cluster (control plane)

```bash
aws eks create-cluster \
  --name my-cluster \
  --region us-east-1 \
  --role-arn arn:aws:iam::123456789012:role/EKSClusterRole \
  --resources-vpc-config subnetIds=subnet-abc123,subnet-def456,securityGroupIds=sg-123456
```

🔍 **Explanation**

* `--role-arn` → IAM role that EKS control plane will use
* `--resources-vpc-config` → Networking details (subnets + security groups)

This only creates the **control plane** (no worker nodes yet).

---

### Step 2. Create a Node Group (worker nodes)

```bash
aws eks create-nodegroup \
  --cluster-name my-cluster \
  --nodegroup-name my-nodes \
  --scaling-config minSize=1,maxSize=3,desiredSize=2 \
  --disk-size 20 \
  --instance-types t3.medium \
  --subnets subnet-abc123 subnet-def456 \
  --node-role arn:aws:iam::123456789012:role/EKSNodeRole
```

🔍 **Explanation**

* Creates EC2 instances as worker nodes
* Attaches them to the cluster

---

### Step 3. Update kubeconfig

```bash
aws eks update-kubeconfig --region us-east-1 --name my-cluster
```

---

### Step 4. Verify

```bash
kubectl get nodes
```

✅ **Result**:

* Cluster with worker nodes ready for workloads

---

# ⚡ Comparison of Methods

| Method           | Difficulty | Speed  | Flexibility | Best For                     |
| ---------------- | ---------- | ------ | ----------- | ---------------------------- |
| `eksctl`         | Easy       | Fast   | Medium      | Beginners & most use cases   |
| AWS Console (UI) | Medium     | Medium | Low         | Learning / Visual setup      |
| AWS CLI          | Hard       | Slow   | High        | Advanced setups / automation |

---

👉 So in short:

* Use **`eksctl`** for quick setup.
* Use **Console** if you want to see things visually.
* Use **AWS CLI** when you need full control or want to script everything.

---


