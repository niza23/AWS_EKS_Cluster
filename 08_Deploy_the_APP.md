
## ⚡1️⃣ What is the kubeconfig file?

* **kubeconfig** is a YAML file that contains:

  * Cluster endpoint (`API server URL`)
  * Cluster CA certificate (`certificate-authority-data`)
  * Authentication info (how to connect, e.g., token, IAM role, OIDC)
  * Contexts (so `kubectl` knows which cluster to talk to)

* Example location:

  ```
  ~/.kube/config
  ```

---

## 2️⃣ Why do you need it?

* `kubectl` (or other Kubernetes clients) **doesn’t know how to find your cluster automatically**.
* kubeconfig tells `kubectl`:

  * *“This is the cluster endpoint”*
  * *“Use this authentication method”*
  * *“Use this namespace/context by default”*

Without it, commands like:

```bash
kubectl get nodes
kubectl get pods --all-namespaces
```

…will fail.

---

## 3️⃣ How do you get it?

* For EKS, you generate/update it using **AWS CLI**:

```bash
aws eks update-kubeconfig --name my-cluster --region us-east-1
```

* This automatically writes your kubeconfig locally (usually at `~/.kube/config`) or merges with an existing one.

---
---


## ⚡1️⃣ What is a Fargate Profile?

* **Fargate profile** defines **which pods run on AWS Fargate** instead of EC2 nodes.
* It maps **Kubernetes namespaces and labels** to Fargate.
* AWS handles provisioning, scaling, and patching the underlying compute — **you don’t see EC2 nodes**.

### Key Points:

| Feature                 | Explanation                                               |
| ----------------------- | --------------------------------------------------------- |
| **Namespace selection** | Only pods in specified namespaces can run on this profile |
| **Label selection**     | Optionally select pods by labels (e.g., `app=frontend`)   |
| **Pod execution role**  | IAM role that pods assume to access AWS services          |
| **Compute management**  | AWS manages CPU, memory, scaling automatically            |

💡 **Example:** You want all pods in the `default` namespace to run on Fargate.

* You create a Fargate profile with `namespace=default`.
* Any pod in `default` automatically runs on Fargate.

```
+------------------------+
|   Kubernetes Cluster   |
+------------------------+
        |
        v
   +------------+       +------------+
   | Namespace: |       | Namespace: |
   |  default   |       |   dev      |
   +------------+       +------------+
        |                   |
        v                   v
+----------------+    +----------------+
| Fargate Profile|    | No Fargate     |
|  (default)     |    | pods run on EC2|
+----------------+    +----------------+
        |
        v
+----------------+
| Pods on Fargate|
+----------------+
```
* Pods in a namespace match a Fargate profile → run serverless.
* Pods in other namespaces without a profile → need EC2 nodes.

---

## 3️⃣ Creating a Fargate Profile

You can create it via **`eksctl`** or **AWS Console**.

### **Option A: Using `eksctl`**

```bash
eksctl create fargateprofile \
  --cluster my-cluster \
  --name my-fargate-profile \
  --namespace default \
  --labels app=frontend \
  --region us-east-1
```

* `--cluster` → your EKS cluster name
* `--name` → name of the Fargate profile
* `--namespace` → Kubernetes namespace(s) for this profile
* `--labels` → optional pod label selector
* `--region` → AWS region

✅ This creates the profile and automatically provisions Fargate resources when pods match.

---
---


## ⚡1️⃣ The Command

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

### What it means:

* **`kubectl apply`** → This tells Kubernetes: *“Apply this configuration to the cluster. Create or update resources as needed.”*
* **`-f <file>`** → Specifies the **file or URL** containing Kubernetes manifests (YAML files).
* **The URL** → Points to a pre-made example provided by the **AWS Load Balancer Controller team**, version 2.5.4.

---

## 2️⃣ What’s Inside the YAML (`2048_full.yaml`)

This YAML contains **multiple Kubernetes resources** for a demo application (the 2048 game) including:

1. **Deployment**

   * Specifies the container image and how many pods to run.
   * Ensures the app is running and can scale.

2. **Service**

   * Exposes the deployment internally or externally.
   * Usually creates a ClusterIP, NodePort, or LoadBalancer service type.

3. **Ingress**

   * Handles **HTTP routing** from outside the cluster to your service.
   * Integrates with AWS **Application Load Balancer (ALB)** via the **AWS Load Balancer Controller**.
   * Allows you to access the app using a URL (DNS).

---

## 3️⃣ How it Works With EKS + Fargate

* The deployment will create **pods on Fargate** (if your namespace matches the Fargate profile).
* The service tells Kubernetes how to route traffic **to those pods**.
* The ingress communicates with the **AWS Load Balancer Controller**, which:

  1. Creates an **ALB** in your VPC.
  2. Configures routing rules to forward traffic to your service pods.

---

## 4️⃣ Verification

After applying the YAML:

```bash
kubectl get deployments
kubectl get pods
kubectl get services
kubectl get ingress
```

* **Deployment** → should show 1 or more pods running.
* **Pods** → should be in `Running` state.
* **Service** → check `EXTERNAL-IP` (for LoadBalancer type).
* **Ingress** → will show a hostname (AWS ALB DNS) you can use to access the app.

---

## 5️⃣ Text Diagram

```
[Internet]
    |
    v
[AWS ALB] <-- created by AWS Load Balancer Controller
    |
    v
[Kubernetes Ingress] -- routes traffic -->
    |
    v
[Kubernetes Service] -- forwards to -->
    |
    v
[Kubernetes Deployment] -- runs pods on Fargate -->
    |
    v
[2048 App Pods]
```

---

✅ **Summary:**

* This command deploys a **demo app** with full Kubernetes resources.
* It demonstrates how **pods (on Fargate)**, **services**, **ingress**, and **AWS ALB** work together.
* Using the **YAML from AWS repo** saves you from writing everything manually.

---
