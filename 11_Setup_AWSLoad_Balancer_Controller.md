

# 📝 Steps to Setup AWS Load Balancer Controller 

### 1️⃣ **Download IAM Policy**

* The controller needs permissions to create AWS resources (ALB, Target Groups, Security Groups).
* AWS provides a ready-made JSON policy with all required permissions.
* Command just downloads that JSON file to your machine.

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.11.0/docs/install/iam_policy.json
```

---

### 2️⃣ **Create IAM Policy**

* You upload that JSON policy into AWS as a named IAM policy.
* This makes it available to attach to roles.

```bash
aws iam create-policy \
  --policy-name AWSLoadBalancerControllerIAMPolicy \
  --policy-document file://iam_policy.json
```

---

### 3️⃣ **Create IAM Role + Service Account (IRSA)**

* The controller itself runs inside your Kubernetes cluster as a **pod**.
* That pod needs AWS permissions → but instead of giving static credentials, we use **IRSA (IAM Role for Service Accounts)**.
* This links:

  * Kubernetes service account (`aws-load-balancer-controller`)
  * IAM Role (with policy you created above)

So when the pod runs, it automatically assumes that IAM role.

```bash
eksctl create iamserviceaccount \
  --cluster=<your-cluster-name> \
  --namespace=kube-system \
  --name=aws-load-balancer-controller \
  --role-name AmazonEKSLoadBalancerControllerRole \
  --attach-policy-arn=arn:aws:iam::<your-aws-account-id>:policy/AWSLoadBalancerControllerIAMPolicy \
  --approve
```

---

### 4️⃣ **Install Controller via Helm**

* Helm = package manager for Kubernetes.
* AWS publishes the Load Balancer Controller as a Helm chart.
* You add the repo, update, and install it into your cluster.

```bash
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks

helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system \
  --set clusterName=<your-cluster-name> \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller \
  --set region=<your-region> \
  --set vpcId=<your-vpc-id>
```

* This deploys the controller as a pod inside the `kube-system` namespace.
* It will now watch for `Ingress` resources and automatically create **ALBs** in your AWS account.

---

### 5️⃣ **Verify Installation**

* Check if the controller is running:

```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```

If it shows **READY**, the controller is working.

---
## You can check from the UI or console if the ALB has been created or not.
```bash
kubectl get ingress -n <name>
```
----> You will get the ADDRESS, you can access the application using http://<ADDRESS>

# ✅ Summary Flow

```
You (kubectl apply Ingress)
        |
        v
AWS Load Balancer Controller Pod (inside EKS, with IAM Role via IRSA)
        |
        v
Uses AWS API with IAM permissions
        |
        v
Creates Application Load Balancer (ALB)
 - Security Groups
 - Target Groups
 - Listener Rules
        |
        v
Routes external traffic -> your Kubernetes Service -> your Pods
```

---
