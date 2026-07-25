# Kubernetes with Minikube on Windows

This guide explains how to install and run **Kubernetes locally on Windows** using **Minikube**, **Docker Desktop**, and **Oracle VirtualBox**.

> **Prerequisites**
>
> Install the following software before continuing:
>
> * Docker Desktop
> * Oracle VirtualBox
> * Windows PowerShell (Run as Administrator)

---

# Step 1: Install Chocolatey

Open **Windows PowerShell as Administrator** and run the following command:

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

Verify Chocolatey is installed:

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

Create a file named:

```
config
```

---

# Step 4: Install Minikube

Open **PowerShell as Administrator** again.

Create the Minikube directory and download Minikube:

```powershell
New-Item -Path 'C:\' -Name 'minikube' -ItemType Directory -Force

$ProgressPreference = 'SilentlyContinue'
Invoke-WebRequest -OutFile 'C:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
```

---

# Step 5: Add Minikube to the System PATH

Run the following command:

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

Restart PowerShell after updating the PATH.

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

If needed, start Minikube again:

```powershell
minikube start
```

Open the Kubernetes Dashboard:

```powershell
minikube dashboard
```

---

# Part 1: Deploy an NGINX Application

Create an NGINX deployment:

```powershell
kubectl create deployment my-nginx --image=nginx:latest
```

Check the running pods:

```powershell
kubectl get pods
```

Expose the deployment:

```powershell
kubectl expose deployment my-nginx --port=80 --type=LoadBalancer
```

View the services:

```powershell
kubectl get services
```

Access the application:

```powershell
minikube service my-nginx
```

---

# Part 2: Practice Kubernetes Commands

Start or stop Minikube:

```powershell
minikube start
```

```powershell
minikube delete
```

Check Minikube status:

```powershell
minikube status
```

Open the Kubernetes Dashboard:

```powershell
minikube dashboard
```

Create a deployment (replace `<image-link>` with your Docker image):

```powershell
kubectl create deployment my-app --image=<image-link>
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

Access the application:

```powershell
minikube service my-app
```

View services:

```powershell
kubectl get services
```

Delete the deployment:

```powershell
kubectl delete deployment my-app
```

---

# Useful Commands

| Command                   | Description                   |
| ------------------------- | ----------------------------- |
| `minikube start`          | Start the Minikube cluster    |
| `minikube stop`           | Stop the cluster              |
| `minikube delete`         | Delete the cluster            |
| `minikube status`         | View cluster status           |
| `minikube dashboard`      | Open the Kubernetes Dashboard |
| `kubectl get pods`        | List running pods             |
| `kubectl get deployments` | List deployments              |
| `kubectl get services`    | List services                 |
| `kubectl cluster-info`    | Display cluster information   |

---

## Troubleshooting

* Always run **PowerShell as Administrator**.
* Make sure **Docker Desktop** is running before starting Minikube.
* Ensure virtualization is enabled in your BIOS if using Oracle VirtualBox.
* Restart PowerShell after adding Minikube to your system PATH.
* If `kubectl` or `minikube` is not recognized, verify that the installation completed successfully and that the PATH is configured correctly.











download docker and also orcale vitual box  so it can work easily 

open windows powershell and run as adminstrator and then paste ther 

Install on Windows using Chocolatey

https://chocolatey.org/


Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))


choco 


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

