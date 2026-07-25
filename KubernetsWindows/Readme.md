
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

