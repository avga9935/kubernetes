# Kubernetes Cluster Setup Guide

This guide provides step-by-step instructions to set up a Kubernetes cluster with one master node and one or more worker nodes. Please follow these instructions carefully.

## Prerequisites

Before you begin, ensure you have the following:

- A Linux machine for the master node.
- One or more Linux machines for worker nodes.
- Internet access on all machines.

## Step 1: Update and Upgrade

On all nodes (master and workers), update and upgrade the system packages:

```bash
sudo apt update
sudo apt upgrade -y
```
## Step 2: Disable Swap
Disable swap on all nodes:
```
bash
sudo swapoff -a
```
## Step 3: Update /etc/fstab
On all nodes, comment out the swap line in /etc/fstab.
```
bash
sudo nano /etc/fstab
```
## Step 4: Set Hostname
On all nodes, assign a hostname:
```
bash
sudo nano /etc/hostname
```
##Step 5: Configure Network Interfaces
On all nodes, add the following lines to /etc/network/interfaces:
```
bash
auto enp0s8
iface enp0s8 inet static
address 192.168.1.x  # Replace x with a unique IP for each node
```
## Step 6: Allow SSH Access
On all nodes, allow SSH access and install OpenSSH Server:
```
bash
sudo ufw allow 22
sudo apt-get install -y openssh-server
```
## Step 7: Generate SSH Keys
On all nodes, generate SSH keys:
```
bash
ssh-keygen
```
## Step 8: Copy SSH Keys
Copy SSH keys from the master node to all worker nodes and vice versa to the .ssh/authorized_keys folder so we can ssh easily without issues:

## Step 9: Install Docker
On all nodes, install Docker:
```
bash
sudo apt-get update
sudo apt-get install -y curl docker.io
sudo systemctl start docker
sudo systemctl enable docker
sudo usermod -aG docker $USER
```
## Step 10: Install Kubernetes Tools
On all nodes, install Kubernetes tools:
```
bash
sudo apt-get update
sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubeadm kubelet kubectl
```

# TILL STEP 10 EVERYTHING NEEDS TO BE FOLLOWED ON BOTH MASTER AND WORKER NODE AND NOW ONLY ON MASTER NODE

## Step 11: Initialize Kubernetes (Master Node Only)
On the master node, initialize Kubernetes:
```
bash
sudo kubeadm init
```
## Step 12: Configure kubectl (Master Node Only)
On the master node, configure kubectl:
```
bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
## Step 13: Deploy Weave Network (Master Node Only)
# WITHOUT THIS NETWORK OUR NODES OR PODS WILL KEEP ON GIVING ERRORS RELATED TO NETWORK AND WON'T GET IN READY STATE
On the master node, deploy the Weave network:
```
bash
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```
## Step 14: Verify Cluster
On the master node, verify that the cluster is up and running:
```
bash
kubectl get pods -n kube-system
kubectl get nodes
```
## Step 15: Create a Namespace (Master Node Only)
On the master node, create a namespace (e.g., "wordpress"):
```
bash
kubectl create namespace wordpress
```
