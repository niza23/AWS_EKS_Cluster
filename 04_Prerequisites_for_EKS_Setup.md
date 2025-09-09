


# 📘 Prerequisites for Setting up EKS

Before creating an Amazon EKS (Elastic Kubernetes Service) cluster, you need to install a few tools on your local machine.
Each tool has a purpose — AWS CLI helps you talk to AWS, eksctl helps you create/manage clusters, and kubectl helps you manage Kubernetes itself.

---

## 1. **kubectl** – Kubernetes Command Line Tool

🔹 **What is it?**

* `kubectl` is the official command-line tool to interact with Kubernetes clusters.
* You’ll use it to deploy applications, check cluster resources, debug issues, and manage workloads.

🔹 **Why do we need it?**

* Without `kubectl`, you cannot talk to the Kubernetes API server (which is the brain of the cluster).
* It’s the main way you’ll interact with your EKS cluster once it’s running.

🔹 **Install Commands**

**Linux / macOS**

```bash
# Download latest stable release
curl -LO "https://dl.k8s.io/release/$(curl -s https://dl.k8s.io/release/stable.txt)/bin/$(uname | tr '[:upper:]' '[:lower:]')/amd64/kubectl"

# Make it executable
chmod +x kubectl

# Move to /usr/local/bin
sudo mv kubectl /usr/local/bin/

# Verify installation
kubectl version --client
```

**Windows (PowerShell)**

```powershell
curl.exe -LO "https://dl.k8s.io/release/v1.29.0/bin/windows/amd64/kubectl.exe"
Move-Item .\kubectl.exe C:\Windows\System32\
kubectl version --client
```

---

## 2. **AWS CLI** – AWS Command Line Interface

🔹 **What is it?**

* The AWS CLI lets you interact with AWS services (like EC2, S3, IAM, and EKS) directly from your terminal.

🔹 **Why do we need it?**

* EKS runs inside AWS, so we need the AWS CLI to authenticate with our AWS account and to configure cluster access.
* `aws eks update-kubeconfig` is used to connect your local `kubectl` with the EKS cluster.

🔹 **Install Commands**

**Linux**

```bash
# Download AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install

# Verify installation
aws --version
```

**macOS (with Homebrew)**

```bash
brew install awscli
aws --version
```

**Windows**

* Download and install from: [AWS CLI MSI](https://awscli.amazonaws.com/AWSCLIV2.msi)
* Verify installation:

```powershell
aws --version
```

🔹 **Configuration**

```bash
aws configure
```

It will ask for:

* **AWS Access Key ID**
* **AWS Secret Access Key**
* **Default region** (e.g. `us-east-1`)
* **Output format** (`json`, `table`, or `text`)

---

## 3. **eksctl** – EKS Management Tool

🔹 **What is it?**

* `eksctl` is a simple CLI tool to create and manage EKS clusters.
* It automates a lot of steps (VPC setup, worker nodes, IAM roles), so you don’t have to write long AWS CLI commands.

🔹 **Why do we need it?**

* Without `eksctl`, you would need 20+ manual commands just to create a cluster.
* With `eksctl`, you can spin up a cluster in **1 command**.

🔹 **Install Commands**

**Linux**

```bash
curl -sS "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

**macOS (Homebrew)**

```bash
brew tap eksctl-io/eksctl
brew install eksctl
eksctl version
```

**Windows (Chocolatey)**

```powershell
choco install eksctl
eksctl version
```

---

# ⚡ How These Tools Work Together

```
You (Laptop)
   │
   ├── AWS CLI → Connects to AWS account (IAM credentials, region, etc.)
   │
   ├── eksctl → Creates & manages the EKS cluster (VPC, nodes, IAM roles)
   │
   └── kubectl → Manages workloads inside the Kubernetes cluster (pods, services, deployments)
```

📌 **In short**:

* **AWS CLI** = Your access to AWS.
* **eksctl** = Creates the EKS cluster.
* **kubectl** = Runs and manages applications inside EKS.

---

