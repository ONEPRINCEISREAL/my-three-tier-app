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


### Step 4: Install Docker

Install Docker to build and run containerized applications.

```bash
sudo apt-get update
sudo apt install docker.io -y
sudo chown $USER /var/run/docker.sock
docker ps

### Step 5: Install kubectl

Install the Kubernetes command-line tool to communicate with the cluster.

```bash
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client


### Step 6: Install eksctl

Install the official CLI tool for Amazon EKS.

```bash
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

### Step 7: Setup EKS Cluster

Create the Kubernetes cluster using eksctl. This process usually takes 10–15 minutes.

```bash
eksctl create cluster --name three-tier-cluster --region us-west-2 --node-type t2.medium --nodes-min 2 --nodes-max 2
aws eks update-kubeconfig --region us-west-2 --name three-tier-cluster
kubectl get nodes
