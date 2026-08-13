
# Kubernetes Setup with Docker, kubectl, and Minikube

This guide explains how to install Docker, kubectl, Minikube, and conntrack on Ubuntu/Linux and start a Kubernetes cluster using Minikube.

---

## Prerequisites

Before starting, make sure you have:

- Ubuntu/Linux system
- `sudo` access
- Internet connectivity

---

# 1. Install Docker

Update the package list:

```bash
sudo apt update
```

Install Docker:

```bash
sudo apt install -y docker.io
```

Enable Docker to start automatically:

```bash
sudo systemctl enable docker
```

Start the Docker service:

```bash
sudo systemctl start docker
```

## Allow Current User to Use Docker Without sudo

Add the current user to the Docker group:

```bash
sudo usermod -aG docker $USER
```

Apply the group changes:

```bash
newgrp docker
```

## Verify Docker Installation

Check the Docker version:

```bash
docker --version
```

Example:

```text
Docker version 28.x.x
```

You can also verify that Docker works without `sudo`:

```bash
docker ps
```

---

# 2. Install kubectl

Download the latest stable version of kubectl:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Make the kubectl binary executable:

```bash
chmod +x kubectl
```

Move kubectl to `/usr/local/bin`:

```bash
sudo mv kubectl /usr/local/bin/
```

## Verify kubectl Installation

Run:

```bash
kubectl version --client
```

Example:

```text
Client Version: v1.xx.x
```

---

# 3. Install Minikube

Download the latest Minikube binary:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

Install Minikube:

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

## Verify Minikube Installation

Run:

```bash
minikube version
```

Example:

```text
minikube version: v1.xx.x
```

---

# 4. Install conntrack

Install `conntrack`:

```bash
sudo apt install -y conntrack
```

---

# 5. Start Minikube

Minikube can be started using either the Docker driver or the `none` driver.

## Option 1: Docker Driver (Recommended)

The Docker driver is recommended for most environments.

Start Minikube:

```bash
minikube start --driver=docker
```

Minikube will create and run the Kubernetes cluster using Docker.

---

## Option 2: EC2 Without Virtualization

For older EC2 environments where virtualization is not available, use the `none` driver:

```bash
sudo minikube start --driver=none
```

> **Note:** The `none` driver runs Kubernetes components directly on the host machine and requires `sudo`.

---

# 6. Check Minikube Status

After starting Minikube, check the cluster status:

```bash
minikube status
```

Expected output:

```text
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

All components should show the expected running/configured status.

---

# 7. Test Kubernetes

Check the Kubernetes nodes:

```bash
kubectl get nodes
```

Expected output:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   ...   v1.xx.x
```

The `STATUS` should be:

```text
Ready
```

If the Minikube node is `Ready`, your Kubernetes cluster is running successfully.

---

# Complete Installation

The following commands contain the complete setup in the correct order.

## Install Docker

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
newgrp docker
```

Verify:

```bash
docker --version
```

---

## Install kubectl

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

Verify:

```bash
kubectl version --client
```

---

## Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Verify:

```bash
minikube version
```

---

## Install conntrack

```bash
sudo apt install -y conntrack
```

---

## Start Minikube

### Docker Driver

```bash
minikube start --driver=docker
```

### EC2 Without Virtualization

```bash
sudo minikube start --driver=none
```

---

## Verify Minikube

```bash
minikube status
```

Expected:

```text
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

---

## Verify Kubernetes

```bash
kubectl get nodes
```

Expected:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   ...   v1.xx.x
```

---

# Verification Commands

After completing the installation, run:

```bash
docker --version
```

```bash
kubectl version --client
```

```bash
minikube version
```

```bash
minikube status
```

```bash
kubectl get nodes
```

---

# Useful Commands

## Check Docker

```bash
docker --version
```

## Check Running Docker Containers

```bash
docker ps
```

## Check kubectl

```bash
kubectl version --client
```

## Check Minikube Version

```bash
minikube version
```

## Check Minikube Status

```bash
minikube status
```

## Check Kubernetes Nodes

```bash
kubectl get nodes
```

## Check All Kubernetes Pods

```bash
kubectl get pods -A
```

## Stop Minikube

```bash
minikube stop
```

## Start Minikube Again

```bash
minikube start
```

## Delete Minikube

```bash
minikube delete
```

## View Minikube Logs

```bash
minikube logs
```

## Open Kubernetes Dashboard

```bash
minikube dashboard
```

---

# Troubleshooting

## Docker Permission Denied

If you get a Docker permission error such as:

```text
permission denied while trying to connect to the Docker daemon
```

Add your current user to the Docker group:

```bash
sudo usermod -aG docker $USER
```

Then run:

```bash
newgrp docker
```

Verify:

```bash
docker ps
```

If the problem continues, log out and log back in, then try again.

---

## Docker Service Is Not Running

Check the Docker service:

```bash
sudo systemctl status docker
```

Start Docker:

```bash
sudo systemctl start docker
```

Enable Docker at boot:

```bash
sudo systemctl enable docker
```

Verify:

```bash
docker ps
```

---

## Minikube Is Not Running

Check Minikube:

```bash
minikube status
```

If using the Docker driver, make sure Docker is running:

```bash
sudo systemctl status docker
```

Then start Minikube:

```bash
minikube start --driver=docker
```

---

## Kubernetes Node Is Not Ready

Check the nodes:

```bash
kubectl get nodes
```

Check all Kubernetes pods:

```bash
kubectl get pods -A
```

Check Minikube logs:

```bash
minikube logs
```

---

# Expected Final Result

After completing all steps, the following commands should work successfully:

```bash
docker --version
kubectl version --client
minikube version
minikube status
kubectl get nodes
```

Minikube status should show:

```text
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured
```

And Kubernetes should show the Minikube node as:

```text
NAME       STATUS   ROLES           AGE   VERSION
minikube   Ready    control-plane   ...   v1.xx.x
```

Your Kubernetes environment is now ready to use.





















===========================================================
1. Install Docker
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

# Allow current user to use Docker without sudo
sudo usermod -aG docker $USER
newgrp docker



Verify:

docker --version



2. Install kubectl

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl

sudo mv kubectl /usr/local/bin/


Verify:

kubectl version --client


3. Install Minikube

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube


minikube version


4. Install conntrack

sudo apt install -y conntrack



5. Start Minikube
If using Docker Driver (Recommended)


minikube start --driver=docker


If using EC2 without virtualization (Older method)

sudo minikube start --driver=none

6. Check Status

minikube status

Expected output:

host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured


7. Test Kubernetes
kubectl get nodes




# Complete Installation Commands



====================================================================================================================================


sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
newgrp docker

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

sudo apt install -y conntrack

minikube start --driver=docker


#


ANberlin
