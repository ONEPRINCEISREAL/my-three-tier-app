# 🚀 Three-Tier Web Application Deployment on AWS EKS

This repository contains a comprehensive guide and the necessary configuration files to provision infrastructure and deploy a Three-Tier Web Application on Amazon EKS (Elastic Kubernetes Service).

Getting started with this project involves setting up IAM, provisioning EC2 infrastructure, configuring Kubernetes tools, creating an EKS cluster, and deploying an AWS Load Balancer.

---

## 📋 Prerequisites

### Step 1: IAM Configuration
1. Go to the AWS IAM Console.
2. Create a new user named `eks-admin` with **AdministratorAccess** policies attached.
3. Generate **Security Credentials**: Download the Access Key and Secret Access Key.

### Step 2: EC2 Setup
1. Launch an Ubuntu EC2 instance in your preferred region (e.g., `us-west-2`).
2. SSH into the instance from your local machine to begin the setup.

---

## 🛠️ Environment Setup

### Step 3: Install AWS CLI v2
Update your system and install the latest AWS CLI to interact with your AWS account.

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip -y
unzip awscliv2.zip
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
aws configure
````

*(Enter the IAM credentials generated in Step 1 when prompted.)*

---

### Step 4: Install Docker

Install Docker to build and run containerized applications.

```bash
sudo apt-get update
sudo apt install docker.io -y
sudo chown $USER /var/run/docker.sock
docker ps
```

---

### Step 5: Install kubectl

Install the Kubernetes command-line tool to communicate with the cluster.

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client
```

---

### Step 6: Install eksctl

Install the official CLI tool for Amazon EKS.

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version
```

---

## ☁️ Cluster Provisioning & Deployment

### Step 7: Setup EKS Cluster

Create the Kubernetes cluster using `eksctl`. This process usually takes 10–15 minutes.

```bash
eksctl create cluster --name three-tier-cluster --region us-west-2 --node-type t2.medium --nodes-min 2 --nodes-max 2
aws eks update-kubeconfig --region us-west-2 --name three-tier-cluster
kubectl get nodes
```

---

### Step 8: Run Manifests

Deploy the application components to your newly created cluster.

```bash
kubectl create namespace three-tier
kubectl apply -f .
```

To tear down the manifests later:

```bash
kubectl delete -f .
```

---

## 🌐 Ingress & Load Balancing

### Step 9: Install AWS Load Balancer IAM Policy

Set up the required IAM policies and OIDC provider for the Load Balancer Controller.

```bash
curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json

aws iam create-policy \
    --policy-name AWSLoadBalancerControllerIAMPolicy \
    --policy-document file://iam_policy.json

eksctl utils associate-iam-oidc-provider \
    --region=us-west-2 \
    --cluster=three-tier-cluster \
    --approve
```

> ⚠️ Replace `<YOUR_AWS_ACCOUNT_ID>` with your actual 12-digit AWS Account ID.

```bash
eksctl create iamserviceaccount \
    --cluster=three-tier-cluster \
    --namespace=kube-system \
    --name=aws-load-balancer-controller \
    --role-name AmazonEKSLoadBalancerControllerRole \
    --attach-policy-arn=arn:aws:iam::<YOUR_AWS_ACCOUNT_ID>:policy/AWSLoadBalancerControllerIAMPolicy \
    --approve \
    --region=us-west-2
```

---

### Step 10: Deploy AWS Load Balancer Controller using Helm

Install Helm and deploy the controller.

```bash
sudo snap install helm --classic

helm repo add eks https://aws.github.io/eks-charts
helm repo update eks

helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
  -n kube-system \
  --set clusterName=three-tier-cluster \
  --set serviceAccount.create=false \
  --set serviceAccount.name=aws-load-balancer-controller

kubectl get deployment -n kube-system aws-load-balancer-controller
kubectl apply -f full_stack_lb.yaml
```

---

## 🧹 Cleanup

To avoid incurring unnecessary AWS charges, ensure you clean up the resources once you are done testing.

### 1. Delete the EKS cluster:

```bash
eksctl delete cluster --name three-tier-cluster --region us-west-2
```

### 2. Clean up EC2:

Go to the AWS EC2 console and **Stop** or **Terminate** the Ubuntu instance created in Step 2.

### 3. Clean up Load Balancers:

Delete any Application/Classic Load Balancers created during Steps 9 & 10 from the EC2 console.

### 4. Clean up Security Groups:

Delete the security groups associated with this project in the VPC/EC2 dashboard.

---

## 👨‍💻 Author

**Prince Singh Chauhan**

* 🌐 **Portfolio:** [https://princesinghchauhan.qzz.io](https://princesinghchauhan.qzz.io)
* 💼 **LinkedIn:** [https://www.linkedin.com/in/prince-chauhan-20901930b](https://www.linkedin.com/in/prince-chauhan-20901930b)
* 💻 **GitHub:** [https://github.com/ONEPRINCEISREAL](https://github.com/ONEPRINCEISREAL)

```

---

Agar chahe to mai isko **aur professional bana du (badges + architecture diagram + screenshots + resume-level)** bhi bana sakta hoon 👍
```
