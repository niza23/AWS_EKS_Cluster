
# EKS Cluster Setup Guide

This guide provides step-by-step instructions to create and manage an **Amazon EKS (Elastic Kubernetes Service)** cluster using the AWS CLI and `eksctl`.

---

## Prerequisites

Before creating an EKS cluster, ensure the following tools are installed:

* **AWS CLI** (configured with IAM user/role that has EKS permissions)

  ```bash
  aws configure
  ```

* **eksctl** (command-line utility for creating EKS clusters)
  [Installation guide](https://eksctl.io/introduction/installation/)

* **kubectl** (Kubernetes command-line tool)
  [Installation guide](https://kubernetes.io/docs/tasks/tools/)

* An **IAM Role/User** with required permissions for EKS, EC2, VPC, and CloudFormation.

---

## Cluster Creation
