# 🚀 Running Minikube Kubernetes Cluster on AWS EC2 Using Docker Driver

This guide explains how to install and run **Minikube Kubernetes cluster on an AWS EC2 Ubuntu instance**.

Because AWS EC2 does not support nested virtualization by default, we will use the **Docker driver** with Minikube.

---

# 1. EC2 Instance Requirements

Before installing Minikube, make sure your EC2 instance has enough resources.

## Recommended Instance

```
Instance Type: t3.medium or higher
CPU:           2 vCPU
RAM:           4GB
Operating OS: Ubuntu
```

⚠️ Important:

- `t2.micro` does not have enough CPU and memory for Minikube.
- EC2 does not support nested virtualization by default.
- Do **not use VirtualBox or KVM drivers**.
- Use the **Docker driver** for Minikube.

---

# 2. Update System and Install Docker

Update Ubuntu packages:

```bash
sudo apt update && sudo apt upgrade -y
```

Install Docker:

```bash
sudo apt install -y docker.io
```

Start and enable Docker service:

```bash
sudo systemctl enable --now docker
```

Add current user to Docker group:

```bash
sudo usermod -aG docker $USER
```

Apply Docker group changes:

```bash
newgrp docker
```

Or logout and login again.

---

# Verify Docker Installation

Run:

```bash
docker run hello-world
```

Expected output:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

Docker is now running without `sudo`.

---

# 3. Install kubectl

`kubectl` is the Kubernetes command-line tool used to communicate with the cluster.

Download kubectl:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Install kubectl:

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Verify installation:

```bash
kubectl version --client
```

Example:

```
Client Version: v1.xx.x
```

---

# 4. Install Minikube

Download Minikube:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

Install Minikube:

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Verify Minikube:

```bash
minikube version
```

Example:

```
minikube version: v1.38.1
```

---

# 5. Start Minikube Using Docker Driver

Start Kubernetes cluster:

```bash
minikube start --driver=docker --cpus=2 --memory=3800
```

Explanation:

| Option | Description |
|---|---|
| `--driver=docker` | Runs Minikube inside Docker |
| `--cpus=2` | Allocates 2 CPU cores |
| `--memory=3800` | Allocates 3.8GB RAM |

---

# ❌ Common Error: Running Minikube as Root

Error:

```
The "docker" driver should not be used with root privileges.

Exiting due to DRV_AS_ROOT
```

Example:

```bash
root@ip-172-31-35-92:/home/ubuntu#
```

Minikube does not allow Docker driver with the root user.

---

# ✅ Solution: Switch to Ubuntu User (Recommended)

Exit from root:

```bash
exit
```

OR switch user manually:

```bash
su - ubuntu
```

Check current user:

```bash
whoami
```

Output:

```
ubuntu
```

---

# Add Ubuntu User to Docker Group

Run:

```bash
sudo usermod -aG docker ubuntu
```

Refresh permissions:

```bash
newgrp docker
```

Check groups:

```bash
groups
```

Output should contain:

```
docker
```

---

# Verify Docker Without sudo

Run:

```bash
docker run hello-world
```

If Docker works, start Minikube:

```bash
minikube start --driver=docker --cpus=2 --memory=3800
```

---

# Option 2: Force Minikube as Root

If you still want to run Minikube as root:

```bash
minikube start --driver=docker --cpus=2 --memory=3800 --force
```

⚠️ This is not recommended.

The recommended approach is always:

```
root user
   |
   ↓
switch to ubuntu user
   |
   ↓
run minikube using docker driver
```

---

# 6. Verify Kubernetes Cluster

Check Minikube status:

```bash
minikube status
```

Expected:

```
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

---

Check Kubernetes nodes:

```bash
kubectl get nodes
```

Expected:

```
NAME       STATUS   ROLES           AGE
minikube   Ready    control-plane   5m
```

---

# 7. Check Kubernetes Pods

View all pods:

```bash
kubectl get pods -A
```

Example:

```
kube-system
 ├── coredns
 ├── kube-proxy
 └── storage-provisioner
```

---

# Architecture

```
              AWS EC2 t3.medium
                     |
                     |
              Ubuntu Server
                     |
                     |
                  Docker
                     |
                     |
                Minikube
                     |
                     |
              Kubernetes Cluster
                     |
                     |
                   Pods
                     |
                     |
              Containers
```

---

# Useful Commands

## Stop Minikube

```bash
minikube stop
```

## Start Minikube Again

```bash
minikube start --driver=docker
```

## Delete Cluster

```bash
minikube delete
```

## Check Cluster Status

```bash
minikube status
```

## View Nodes

```bash
kubectl get nodes
```

## View All Pods

```bash
kubectl get pods -A
```

---

# 🎯 Conclusion

Successfully installed:

✅ Docker  
✅ kubectl  
✅ Minikube  
✅ Kubernetes Cluster on AWS EC2  

This setup can be used for:

- Kubernetes learning
- DevOps practice
- Testing deployments
- Running Kubernetes workloads on AWS





################################################################


















1. Instance requirements
Use at least a t3.medium (2 vCPU, 4GB RAM) — t2.micro will not have enough resources.
Since EC2 doesn't support nested virtualization by default, use the Docker driver for minikube (not VirtualBox/KVM).

2. Update system and install Docker
bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io
sudo systemctl enable --now docker
sudo usermod -aG docker $USER
newgrp docker   # or log out/in to apply group change



Verify Docker works without sudo:


docker run hello-world


3. Install kubectl


curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
kubectl version --client

4. Install minikube
bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
minikube version


5. Start minikube (Docker driver)
bash
minikube start --driver=docker --cpus=2 --memory=3800


error 


oot@ip-172-31-35-92:/home/ubuntu# minikube start --driver=docker --cpus=2 --memory=3800
* minikube v1.38.1 on Ubuntu 26.04 (xen/amd64)
* Using the docker driver based on user configuration
* The "docker" driver should not be used with root privileges. If you wish to continue as root, use --force.
* If you are running minikube within a VM, consider using --driver=none:
*   https://minikube.sigs.k8s.io/docs/reference/drivers/none/
X Exiting due to DRV_AS_ROOT: The "docker" driver should not be used with root privileges.
root@ip-172-31-35-92:/home/ubuntu#


# Exit root and switch to the ubuntu user
exit
# or if you're in an SSH session already as root, just:
su - ubuntu


sudo usermod -aG docker ubuntu


whoami          # should show "ubuntu"
groups          # should include "docker"
docker run hello-world


minikube start --driver=docker --cpus=2 --memory=3800


you can force it:


minikube start --driver=docker --cpus=2 --memory=3800 --force
