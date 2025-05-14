
# 🚀 EKS Management Host Setup on AWS (Ubuntu EC2)

This guide provides step-by-step instructions to set up an **EKS Management Host** using an Ubuntu EC2 instance. You'll learn to install required tools, configure IAM, and create/delete EKS clusters with `eksctl`.

---

## ✅ Prerequisites

- AWS account with admin access
- EC2 key pair for SSH access
- Basic CLI and AWS IAM knowledge

---

## 🔹 Step 1: Launch Ubuntu EC2 (EKS Management Host)

### 1. Launch Instance
- **Instance Type:** `t2.micro` (Free Tier)
- **AMI:** Ubuntu 20.04 or later
- **Security Group:** Allow ports `22` (SSH), `80`, and `443`
- **Key Pair:** Use an existing or create a new key pair

### 2. Connect via SSH
```bash
ssh -i /path/to/key.pem ubuntu@<EC2-Public-IP>
````

---

## 🔹 Step 2: Install Required Tools

### ✅ Install `kubectl`

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/
kubectl version --short --client
```

> 📌 Optional: Install latest version from AWS Docs

---

### ✅ Install AWS CLI (v2)

```bash
sudo apt update && sudo apt install unzip -y
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
aws --version
```

---

### ✅ Install `eksctl`

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

---

## 🔹 Step 3: Configure IAM Role

### 1. Create IAM Role

* Go to **IAM > Roles > Create Role**
* **Use Case:** EC2
* **Attach Policy:** `AdministratorAccess` (or limited EKS permissions)

### 2. Attach IAM Role to EC2

* Go to **EC2 Console**
* Select your EC2 → **Actions > Security > Modify IAM Role**
* Choose the IAM role (e.g., `eksroleec2`)

---

## 🔹 Step 4: Create EKS Cluster with `eksctl`

### 📌 Syntax

```bash
eksctl create cluster \
--name <cluster-name> \
--region <region-name> \
--node-type <instance-type> \
--nodes-min 2 \
--nodes-max 2 \
--zones <AZ-1>,<AZ-2>
```

### ✅ Example: Mumbai

```bash
eksctl create cluster \
--name om-cluster \
--region ap-south-1 \
--node-type t2.medium \
--zones ap-south-1a,ap-south-1b
```

> ⏳ Creation takes \~5–10 minutes

---

## 🔹 Step 5: Verify Cluster

After creation:

```bash
kubectl get nodes
```

> ✅ You should see 2 worker nodes in `Ready` state.

---

## 🔹 Step 6: Clean Up Resources

To avoid unwanted billing:

```bash
eksctl delete cluster --name om-cluster --region ap-south-1
```

> 💡 Also terminate EC2 instance and delete unused resources (EBS, Load Balancers, IAM Roles, etc.)

---

## 📚 Additional Resources

* 📘 [Amazon EKS Documentation](https://docs.aws.amazon.com/eks/)
* 📘 [eksctl GitHub](https://github.com/weaveworks/eksctl)
* 🔐 [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html)

---

## ✅ Done!

Your EKS Management Host is ready to go! 
Feel free to fork this repo and customize it for your team or training purposes.
