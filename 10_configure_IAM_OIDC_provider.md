

# 🔑 Why We Need IAM OIDC Provider in EKS

When deploying the **AWS Load Balancer Controller** (or any pod that needs AWS permissions), the pod must talk to AWS APIs. For example, the controller needs to:

* Create and manage **Application Load Balancers (ALBs)**
* Configure **Target Groups, Listeners, and Rules**
* Attach load balancers to your **VPC and Subnets**

Normally, Kubernetes pods **cannot call AWS APIs directly**.

---

## 🟡 Solution → IAM OIDC Provider + IRSA

EKS uses an **OpenID Connect (OIDC) identity provider** to allow pods to securely assume AWS IAM roles.

* **OIDC Provider**: Connects Kubernetes service accounts with AWS IAM
* **IAM Role for Service Account (IRSA)**: Grants AWS permissions (like `elasticloadbalancing:*`) to pods
* **Pod (e.g., ALB Controller)**: Uses its service account to get an AWS token and call APIs

### ✅ Why this is better than Access Keys

* No need to store AWS credentials in pods
* IAM roles provide **temporary credentials** → more secure
* Granular access control per pod (least privilege)

---

## 🔵 How It Works (Flow)

```
Pod (ALB Controller)
   |
   v
Kubernetes Service Account
   |
   v
OIDC Token issued by EKS
   |
   v
IAM OIDC Provider (trusts EKS tokens)
   |
   v
IAM Role with Policy (Load Balancer permissions)
   |
   v
AWS APIs (create/manage ALB, Target Groups, etc.)
```

---

## 🛠️ Commands to Configure IAM OIDC Provider

### 1. Set your cluster name

```bash
export cluster_name=demo-cluster
```

### 2. Get the OIDC ID of the cluster (either using CLI or from the AWS UI as well)

```bash
oidc_id=$(aws eks describe-cluster \
  --name $cluster_name \
  --query "cluster.identity.oidc.issuer" \
  --output text | cut -d '/' -f 5)
```

### 3. Check if an OIDC provider is already configured

```bash
aws iam list-open-id-connect-providers | grep $oidc_id | cut -d "/" -f4
```

* If you see an output → provider already exists
* If empty → you need to create one

### 4. Associate OIDC provider with your cluster

```bash
eksctl utils associate-iam-oidc-provider \
  --cluster $cluster_name \
  --approve
```

---

👉 In summary:
**Without OIDC →** Pods cannot create/manage AWS resources.
**With OIDC →** Pods can securely assume IAM roles and access only what they need.

---
