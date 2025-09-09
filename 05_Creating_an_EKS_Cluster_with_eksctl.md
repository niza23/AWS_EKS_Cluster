

# 📘 Creating an EKS Cluster with `eksctl`

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

