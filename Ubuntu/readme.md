# Install Docker, kubectl & Minikube on Ubuntu

This guide explains how to install **Docker**, **kubectl**, and **Minikube** on Ubuntu and start a local Kubernetes cluster.

---

## Prerequisites

- Ubuntu
- Sudo privileges
- Internet connection

---

## Step 1: Install Docker

Update the package list and install Docker.

```bash
sudo apt update
sudo apt install -y docker.io
```

---

## Step 2: Install kubectl

Download the latest stable version of `kubectl`.

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Make the file executable.

```bash
chmod +x kubectl
```

Move it to the system path.

```bash
sudo mv kubectl /usr/local/bin/
```

---

## Step 3: Install Minikube

Download the latest Minikube binary.

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

Install Minikube.

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

---

## Step 4: Install conntrack

Install `conntrack`, which is required for Minikube.

```bash
sudo apt install -y conntrack
```

---

## Step 5: Start Minikube

Start the Kubernetes cluster.

```bash
sudo minikube start --driver=none
```

> **Note:** If you are using Docker as the driver, use:
>
> ```bash
> minikube start --driver=docker
> ```

---

## Step 6: Check Minikube Status

Verify that Minikube is running.

```bash
minikube status
```

---

## Installation Complete

Your local Kubernetes cluster is now ready to use.

Verify the cluster by running:

```bash
kubectl get nodes
```

Expected output:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   xxm   v1.xx.x
```
