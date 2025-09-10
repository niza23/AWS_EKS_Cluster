

# Amazon EKS End-to-End Project

## Project Overview

This project demonstrates **an end-to-end setup and deployment of applications on Amazon EKS (Elastic Kubernetes Service)**. It covers everything from understanding Kubernetes fundamentals, creating and configuring EKS clusters, deploying workloads, to setting up an Application Load Balancer (ALB) for external access.

The goal of this project is to provide a **hands-on experience** of managing Kubernetes in a cloud environment, understanding AWS integration, and deploying applications in a production-ready setup.

---

## 🔹 Project Contents

The repository is structured in a step-by-step manner with individual guides:

| Step | File                                             | Description                                                                                         |
| ---- | ------------------------------------------------ | --------------------------------------------------------------------------------------------------- |
| 1    | 01\_Basics\_of\_EKS.md                           | Introduction to Kubernetes and Amazon EKS. Pros & cons of EKS vs Self-Managed Kubernetes.           |
| 2    | 02\_Ways\_to\_run\_k8s.md                        | Different approaches to run Kubernetes: Self-managed, Kops, and EKS.                                |
| 3    | 03\_Ways\_to\_Run\_Workloads\_in\_Amazon\_EKS.md | Deploying workloads on EKS clusters using pods, deployments, and services.                          |
| 4    | 04\_Prerequisites\_for\_EKS\_Setup.md            | Required tools on local machine: kubectl, AWS CLI, eksctl, and setup instructions.                  |
| 5    | 05\_Creating\_an-EKS\_Cluster\_with\_eksctl.md   | Step-by-step creation of an EKS cluster using eksctl.                                               |
| 6    | 06\_Creating\_an-EKS\_Cluster–3\_Approaches.md   | Detailed explanation of three approaches to create EKS clusters (Control plane management options). |
| 7    | 07\_EKS\_Cluster\_Overview\.md                   | Overview of created EKS cluster, nodes, namespaces, networking, and kubeconfig.                     |
| 8    | 08\_Deploy\_the\_APP.md                          | Instructions to deploy a sample application using Deployment, Service, and Ingress.                 |
| 9    | 09\_App\_Manifest\_files.md                      | Sample manifest files for deployments, services, and ingress rules.                                 |
| 10   | 10\_configure\_IAM\_OIDC\_provider.md            | Configure IAM OIDC provider and IRSA for Kubernetes pods to securely access AWS resources.          |
| 11   | 11\_Setup\_AWSLoad\_Balancer\_Controller.md      | Setup AWS Load Balancer Controller using Helm to make applications accessible externally via ALB.   |

---

## 🔹 Project Workflow (High-Level)

1. **Setup local environment**
   Install `kubectl`, `AWS CLI`, `eksctl` to interact with EKS clusters.

2. **Create EKS Cluster**
   Using `eksctl`, create a cluster with Fargate or EC2 nodes.

3. **Configure IAM & OIDC**

   * Setup IAM OIDC provider for the cluster.
   * Create Service Accounts and IAM roles (IRSA) to give pods secure access to AWS resources.

4. **Deploy Applications**

   * Create deployments, services, and optionally Ingress resources.
   * Pods run your containerized apps.

5. **Setup ALB for external access**

   * Install AWS Load Balancer Controller using Helm.
   * Controller manages ALB and routes external traffic to your pods.

---

## 🔹 Diagrams & Flow

### High-Level EKS + ALB Flow

```
User (Internet)
        |
        v
   +------------+
   |   ALB      |
   +------------+
        |
        v
  Target Group (dynamic pod IPs)
        ^
        |
+-----------------------+
| ALB Controller Pod     |
| Kubernetes Cluster     |
+-----------------------+
        |
        v
   Pod (Application)
```

### IAM OIDC & IRSA Flow

```
ALB Controller Pod
        |
   Service Account (SA)
        |
       IRSA
        |
OIDC Provider (Trust bridge)
        |
    IAM Role + Policy
        |
   AWS APIs (ALB, Target Groups)
```

---

## 🔹 Project Objective

* Learn **Kubernetes fundamentals** in AWS environment
* Understand **EKS cluster management** and scaling
* Deploy **production-ready workloads**
* Configure **Ingress & ALB** for external accessibility
* Implement **security best practices** with IAM, OIDC, and IRSA

---

You can add this as your `README.md` at the root of your repo.

---



