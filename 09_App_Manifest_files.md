
# Sample EKS Application Deployment

This repository demonstrates deploying a **sample application** on Amazon EKS using Kubernetes **Deployment**, **Service**, and optionally **Ingress** (if external access is needed).

It ensures pods run on **Linux nodes**, with support for **Fargate or EC2 nodes**.

---

## Prerequisites

* An **EKS cluster** running (Fargate or EC2)
* **kubectl** installed and configured to access your cluster
* **IAM permissions** to deploy resources in the cluster

Verify access:

```bash
kubectl get nodes
kubectl get namespaces
```

---

## 1️⃣ Deployment

### Step 1: Create `deploy.yml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: eks-sample-linux-deployment
  labels:
    app: eks-sample-linux-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: eks-sample-linux-app
  template:
    metadata:
      labels:
        app: eks-sample-linux-app
    spec:
      affinity:
        nodeAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
            nodeSelectorTerms:
            - matchExpressions:
              - key: kubernetes.io/arch
                operator: In
                values:
                - amd64
                - arm64
      containers:
      - name: nginx
        image: public.ecr.aws/nginx/nginx:1.23
        ports:
        - name: http
          containerPort: 80
        imagePullPolicy: IfNotPresent
      nodeSelector:
        kubernetes.io/os: linux
```

### Step 2: Deploy the application

```bash
kubectl apply -f deploy.yml
kubectl get deployments
kubectl get pods
```

**Explanation:**

* Runs **3 replicas** of an Nginx pod
* Ensures pods run only on **Linux nodes** and compatible CPU architecture
* Uses `IfNotPresent` image pull policy to avoid re-downloading images

---

## 2️⃣ Service

### Step 1: Create `service.yml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: eks-sample-linux-service
  labels:
    app: eks-sample-linux-app
spec:
  selector:
    app: eks-sample-linux-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

> Optional: To expose externally, add `type: LoadBalancer`:

```yaml
spec:
  type: LoadBalancer
```

### Step 2: Deploy the service

```bash
kubectl apply -f service.yml
kubectl get svc
```

**Explanation:**

* Routes traffic to pods labeled `app: eks-sample-linux-app`
* Port 80 is exposed for HTTP traffic
* Service type can be **internal (ClusterIP)** or **external (LoadBalancer)**

---

## 3️⃣ Ingress (Optional)

If you want external HTTP access using **AWS ALB**, create an Ingress resource (requires AWS Load Balancer Controller):

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/examples/2048/2048_full.yaml
```

**Explanation:**

* The Ingress routes traffic from **ALB → Service → Pods**
* Demonstrates full AWS integration with EKS and Fargate

Check ingress:

```bash
kubectl get ingress
```

---

## 4️⃣ Accessing the Application

* **Internal (ClusterIP)**:

```bash
kubectl port-forward svc/eks-sample-linux-service 8080:80
```

* Open browser at `http://localhost:8080`

* **External (LoadBalancer)**:

```bash
kubectl get svc eks-sample-linux-service
```

* Use **EXTERNAL-IP** or DNS provided by AWS


---

## ✅ Summary

* **Deployment:** Manages pods and ensures desired replica count
* **Service:** Exposes pods internally or externally
* **Ingress:** Optional HTTP routing through AWS ALB
* All resources are compatible with **Linux nodes** and **Fargate**

---
 **diagram**

```
                   +----------------+
                   |     User       |
                   |  (Browser/API) |
                   +--------+-------+
                            |
                            v
                    +----------------+
                    |   Ingress      |
                    |  (AWS ALB)     |
                    +--------+-------+
                            |
                            v
                    +----------------+
                    |   Service      |
                    | ClusterIP/     |
                    | LoadBalancer   |
                    +--------+-------+
                            |
                            v
                    +----------------+
                    | Deployment     |
                    | (ReplicaSet)   |
                    +--------+-------+
                            |
          +-----------------+-----------------+
          |                 |                 |
          v                 v                 v
+----------------+  +----------------+  +----------------+
| Pod (nginx)    |  | Pod (nginx)    |  | Pod (nginx)    |
| Linux/Fargate  |  | Linux/Fargate  |  | Linux/Fargate  |
+----------------+  +----------------+  +----------------+
```

### How to read it:

1. **User** → sends HTTP requests via browser or API
2. **Ingress** → routes external requests to the appropriate service using ALB (if configured)
3. **Service** → routes traffic to pods based on labels
4. **Deployment** → ensures the correct number of pods are running
5. **Pods** → actual containers running your application on **Linux nodes or Fargate**

---
