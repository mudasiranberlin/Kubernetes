# Install and Set Up kubectl on Windows

This guide will help you install **kubectl**, **Minikube**, and set up a local Kubernetes environment on Windows.

---

# Prerequisites

Before you begin, install the following software:

- **Docker Desktop** (Recommended)
- **Oracle VirtualBox** (Optional, if you want to use VirtualBox as the Minikube driver)

> **Note:** Docker Desktop is the recommended runtime for Minikube on Windows.

---

# Step 1: Install Chocolatey

Chocolatey is a package manager for Windows that makes installing software easier.

Official Website:

https://chocolatey.org/

## 1. Open PowerShell as Administrator

- Press **Start**
- Search for **Windows PowerShell**
- Right-click **Windows PowerShell**
- Select **Run as Administrator**

---

## 2. Allow PowerShell to Run the Installation Script

Copy and paste the following command into PowerShell:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; `
[System.Net.ServicePointManager]::SecurityProtocol = `
[System.Net.ServicePointManager]::SecurityProtocol -bor 3072; `
iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

Press **Enter** and wait for the installation to finish.

To verify the installation, run:

```powershell
choco
```

or

```powershell
choco -?
```

---

# Step 2: Install kubectl

Install the Kubernetes command-line tool:

```powershell
choco install kubernetes-cli
```

Verify the installation:

```powershell
kubectl version --client
```

---

# Step 3: Configure kubectl

Navigate to your home directory:

```powershell
cd ~
```

Create the Kubernetes configuration folder:

```powershell
mkdir .kube
```

Inside the `.kube` folder, create a file named:

```
config
```

Your Kubernetes cluster configuration will be stored in this file.

---

# Step 4: Start Minikube

Start your local Kubernetes cluster:

```powershell
minikube start
```

Check cluster information:

```powershell
kubectl cluster-info
```

Check Minikube status:

```powershell
minikube status
```

Open the Kubernetes Dashboard:

```powershell
minikube dashboard
```

---

# Part 1: Deploy an NGINX Application

## Create a Deployment

```powershell
kubectl create deployment my-nginx --image=nginx:latest
```

View running Pods:

```powershell
kubectl get pods
```

Expose the deployment:

```powershell
kubectl expose deployment my-nginx --port=80 --type=LoadBalancer
```

View Services:

```powershell
kubectl get services
```

Open the application in your browser:

```powershell
minikube service my-nginx
```

---

# Part 2: Working with Your Own Application

## Start Minikube

```powershell
minikube start
```

## Stop/Delete Minikube

```powershell
minikube stop
```

Delete the cluster:

```powershell
minikube delete
```

Check status:

```powershell
minikube status
```

Open the dashboard:

```powershell
minikube dashboard
```

---

## Create a Deployment

Replace `<YOUR_IMAGE>` with your Docker image.

```powershell
kubectl create deployment my-app --image=<YOUR_IMAGE>
```

View Deployments:

```powershell
kubectl get deployments
```

View Pods:

```powershell
kubectl get pods
```

Expose the Deployment:

```powershell
kubectl expose deployment my-app --type=LoadBalancer --port=80
```

View Services:

```powershell
kubectl get services
```

Access the application:

```powershell
minikube service my-app
```

Delete the deployment when finished:

```powershell
kubectl delete deployment my-app
```

---

# Useful kubectl Commands

| Command | Description |
|----------|-------------|
| `kubectl get pods` | List all Pods |
| `kubectl get deployments` | List Deployments |
| `kubectl get services` | List Services |
| `kubectl cluster-info` | Show cluster information |
| `kubectl version --client` | Show kubectl version |
| `kubectl delete deployment <name>` | Delete a Deployment |

---

# Useful Minikube Commands

| Command | Description |
|----------|-------------|
| `minikube start` | Start the cluster |
| `minikube stop` | Stop the cluster |
| `minikube delete` | Delete the cluster |
| `minikube status` | Check cluster status |
| `minikube dashboard` | Open the Kubernetes Dashboard |
| `minikube service <service-name>` | Open a Service in your browser |

---

# Troubleshooting

If Minikube does not start:

- Ensure **Docker Desktop** is running.
- If using VirtualBox, make sure virtualization is enabled in your BIOS.
- Restart PowerShell as Administrator.
- Verify installations:

```powershell
kubectl version --client
minikube version
docker --version
```

---

## You're Ready!

You now have a local Kubernetes environment using **Minikube** and **kubectl** on Windows. You can deploy, expose, and manage Kubernetes applications directly from your machine.
