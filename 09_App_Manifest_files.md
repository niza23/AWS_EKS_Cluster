

# 🕹️ Deploying the 2048 Game on Amazon EKS

This project demonstrates deploying the **2048 game** on an EKS cluster using Kubernetes resources: **Namespace, Deployment, Service, and Ingress**.

---

## 📂 Files in this Deployment

### 1. Namespace

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: game-2048
```

* Creates a separate Kubernetes **namespace** called `game-2048` to logically isolate this app from other workloads.
* Namespaces help with organizing resources, applying resource quotas, and managing access.

---

### 2. Deployment

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  namespace: game-2048
  name: deployment-2048
spec:
  replicas: 5
  selector:
    matchLabels:
      app.kubernetes.io/name: app-2048
  template:
    metadata:
      labels:
        app.kubernetes.io/name: app-2048
    spec:
      containers:
      - image: public.ecr.aws/l6m2t8p7/docker-2048:latest
        imagePullPolicy: Always
        name: app-2048
        ports:
        - containerPort: 80
```

* Deploys **5 replicas (pods)** of the 2048 game.
* Uses the container image: `public.ecr.aws/l6m2t8p7/docker-2048:latest`.
* Exposes port **80** inside each pod.
* Ensures high availability and load balancing by running multiple replicas.

---

### 3. Service

```yaml
apiVersion: v1
kind: Service
metadata:
  namespace: game-2048
  name: service-2048
spec:
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
  type: NodePort
  selector:
    app.kubernetes.io/name: app-2048
```

* Exposes the deployment inside the cluster via a **Service**.
* Type: `NodePort` → assigns a port on each worker node (or Fargate-managed IP) to forward requests to pods.
* Routes traffic to pods that match the label `app.kubernetes.io/name: app-2048`.

---

### 4. Ingress

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: game-2048
  name: ingress-2048
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: ip
spec:
  ingressClassName: alb
  rules:
    - http:
        paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: service-2048
              port:
                number: 80
```

* Creates an **Ingress** resource that uses the AWS **ALB Ingress Controller**.
* **Annotations**:

  * `alb.ingress.kubernetes.io/scheme: internet-facing` → makes the ALB public.
  * `alb.ingress.kubernetes.io/target-type: ip` → targets pods by IP (works well with Fargate).
* Maps external traffic (`/`) to the `service-2048` on port **80**.

---

## ⚙️ Deployment Steps

1. Save the manifest file locally, e.g., `2048-game.yaml`.
2. Apply it to your EKS cluster:

   ```sh
   kubectl apply -f 2048-game.yaml
   ```
3. Verify resources:

   ```sh
   kubectl get namespaces
   kubectl get deployments -n game-2048
   kubectl get pods -n game-2048
   kubectl get svc -n game-2048
   kubectl get ingress -n game-2048
   ```
4. Once the Ingress is active, AWS will provision an **Application Load Balancer (ALB)**.

   * Get the ALB DNS name:

     ```sh
     kubectl get ingress -n game-2048
     ```
   * Open the DNS in your browser to play the 2048 game 🎮

---

## 📊 Architecture Diagram (Text)

```
            [ User / Browser ]
                     |
                     v
              +---------------+
              |   Ingress     |  <-- AWS ALB (Internet-facing)
              +-------+-------+
                      |
                      v
              +---------------+
              |   Service     |  <-- NodePort
              +-------+-------+
                      |
            ---------------------
            |    |     |    |   |
            v    v     v    v   v
          +-----+   +-----+   +-----+
          | Pod |   | Pod |   | Pod |  <-- 5 replicas of 2048 game
          |     |   |     |   |     |
          +-----+   +-----+   +-----+
```

---
