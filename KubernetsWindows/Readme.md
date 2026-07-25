# Kubernetes with Minikube on Windows

> A complete step-by-step guide to set up **Kubernetes locally on Windows** using **Minikube**, **Docker Desktop**, and **Oracle VirtualBox**.

---

## 📋 Prerequisites

Before starting, install the following software:

* **Docker Desktop**
* **Oracle VirtualBox**
* **Windows PowerShell** (Run as **Administrator**)

> **Note:** Docker Desktop should be running before starting Minikube.

---

# Step 1: Install Chocolatey

Open **Windows PowerShell as Administrator** and run:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

Verify the installation:

```powershell
choco
```

---

# Step 2: Install kubectl

Install the Kubernetes CLI:

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

Open the folder:

```powershell
cd .kube
```

Create a configuration file:

```powershell
New-Item config -ItemType File
```

Verify `kubectl` again:

```powershell
kubectl version --client
```

---

# Step 4: Install Minikube

Open **PowerShell as Administrator** again.

Create the Minikube directory:

```powershell
New-Item -Path 'C:\' -Name 'minikube' -ItemType Directory -Force
```

Download Minikube:

```powershell
$ProgressPreference = 'SilentlyContinue'
Invoke-WebRequest -OutFile 'C:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
```

---

# Step 5: Add Minikube to the System PATH

Run:

```powershell
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)

if ($oldPath.Split(';') -inotcontains 'C:\minikube') {
    [Environment]::SetEnvironmentVariable(
        'Path',
        ('{0};C:\minikube' -f $oldPath),
        [EnvironmentVariableTarget]::Machine
    )
}
```

Restart **PowerShell** after updating the PATH.

---

# Step 6: Start Minikube

Start your local Kubernetes cluster:

```powershell
minikube start
```

Check cluster information:

```powershell
kubectl cluster-info
```

Verify Minikube status:

```powershell
minikube status
```

Open the Kubernetes Dashboard:

```powershell
minikube dashboard
```

---

# 🚀 Part 1: Deploy an NGINX Application

Create a deployment:

```powershell
kubectl create deployment my-nginx --image=nginx:latest
```

View running pods:

```powershell
kubectl get pods
```

Expose the deployment:

```powershell
kubectl expose deployment my-nginx --port=80 --type=LoadBalancer
```

View services:

```powershell
kubectl get services
```

Access the application:

```powershell
minikube service my-nginx
```

---

# 🚀 Part 2: Deploy Your Own Application

Create a deployment using your Docker image:

```powershell
kubectl create deployment my-app --image=<your-docker-image>
```

View deployments:

```powershell
kubectl get deployments
```

View pods:

```powershell
kubectl get pods
```

Expose the deployment:

```powershell
kubectl expose deployment my-app --type=LoadBalancer --port=80
```

Open the application:

```powershell
minikube service my-app
```

View services:

```powershell
kubectl get services
```

Delete the deployment when finished:

```powershell
kubectl delete deployment my-app
```

---

# 📚 Common Minikube Commands

Start the cluster:

```powershell
minikube start
```

Stop the cluster:

```powershell
minikube stop
```

Delete the cluster:

```powershell
minikube delete
```

Check cluster status:

```powershell
minikube status
```

Open the Kubernetes Dashboard:

```powershell
minikube dashboard
```

---

# 📚 Common kubectl Commands

View cluster information:

```powershell
kubectl cluster-info
```

View deployments:

```powershell
kubectl get deployments
```

View pods:

```powershell
kubectl get pods
```

View services:

```powershell
kubectl get services
```

Describe a pod:

```powershell
kubectl describe pod <pod-name>
```

View logs:

```powershell
kubectl logs <pod-name>
```

Delete a deployment:

```powershell
kubectl delete deployment <deployment-name>
```

Delete a service:

```powershell
kubectl delete service <service-name>
```

---

# 📖 Command Summary

| Command                           | Description                  |
| --------------------------------- | ---------------------------- |
| `minikube start`                  | Start the Kubernetes cluster |
| `minikube stop`                   | Stop the cluster             |
| `minikube delete`                 | Delete the cluster           |
| `minikube status`                 | Show cluster status          |
| `minikube dashboard`              | Open Kubernetes Dashboard    |
| `kubectl cluster-info`            | Display cluster information  |
| `kubectl get pods`                | List running pods            |
| `kubectl get deployments`         | List deployments             |
| `kubectl get services`            | List services                |
| `kubectl describe pod <pod-name>` | Show pod details             |
| `kubectl logs <pod-name>`         | View pod logs                |

---

# 🛠 Troubleshooting

### `kubectl` is not recognized

Restart PowerShell or verify that Chocolatey installed successfully.

---

### `minikube` is not recognized

* Verify that `C:\minikube` has been added to the **System PATH**.
* Restart PowerShell after updating the PATH.

---

### Minikube fails to start

* Make sure **Docker Desktop** is running.
* Run PowerShell as **Administrator**.
* Restart your computer if Docker was installed recently.

---

### VirtualBox Issues

If you're using VirtualBox:

* Enable **Virtualization (VT-x / AMD-V)** in your BIOS.
* Install the latest version of Oracle VirtualBox.

---

# 🎉 Congratulations!

You now have a local Kubernetes environment running with **Minikube** on **Windows**.

You can now:

* Deploy applications
* Scale deployments
* Manage Pods and Services
* Explore the Kubernetes Dashboard
* Practice Kubernetes commands locally

Happy Learning! 🚀






download docker and also orcale vitual box  so it can work easily 

open windows powershell and run as adminstrator and then paste ther 

Install on Windows using Chocolatey

https://chocolatey.org/


Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))


choco 


choco install kubernetes-cli

To install kubectl on Windows you can use either Chocolatey package manager, Scoop command-line installer, or winget package manager.

choco
scoop
winget
choco install kubernetes-cli
Test to ensure the version you installed is up-to-date:

kubectl version --client
Navigate to your home directory:

# If you're using cmd.exe, run: cd %USERPROFILE%
cd ~
Create the .kube directory:

mkdir .kube
Change to the .kube directory you just created:

cd .kube
Configure kubectl to use a remote Kubernetes cluster:

New-Item config -type file


Test to ensure the version you installed is up-to-date:

kubectl version --client

Navigate to your home directory:

# If you're using cmd.exe, run: cd %USERPROFILE%
cd ~

Create the .kube directory:

mkdir .kube


Change to the .kube directory you just created:

cd .kube


go there and create a file config



now open powershell again and paste 

New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing

and then again 

$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine)
}


and then 

minikube start


kubectl cluster-info


minikube start

minikube dashboard

now after that

kubectl create deployment my-nginx --image=nginx:latest

kubectl get pods

kubectl expose deployment my-nginx --port=80 --type=LoadBalancer

kubectl get services

minikube service my-nginx

now 2nd Part

minikube start/delete

minikube status

minikube dashboard

kubectl create deployment my-app --image=link kubectl get deployments kubectl get pods kubectl delete deployment my-app

kubectl expose deployment my-app -- type=LoadBalancer --port=80

minikube service my-app

kubectl get services

