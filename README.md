# Kubernetes

This repository contains basic Kubernetes commands and hands-on practice using **Minikube**, **kubectl**, **Docker**, and an **Nginx deployment**.

---

## 📚 Table of Contents

* [Prerequisites](#prerequisites)
* [1. Install Kubernetes Tools on macOS](#1-install-kubernetes-tools-on-macos)
* [2. Start Minikube](#2-start-minikube)
* [3. Create an Nginx Deployment](#3-create-an-nginx-deployment)
* [4. Expose the Application](#4-expose-the-application)
* [5. Common Minikube Commands](#5-common-minikube-commands)
* [6. Create and Delete a Custom Application](#6-create-and-delete-a-custom-application)

---

# Prerequisites

Before starting, install the following tools:

* Homebrew
* kubectl
* Docker Desktop
* Minikube

---

# 1. Install Kubernetes Tools on macOS

## Install Homebrew

Homebrew is a package manager for macOS. It helps us install tools easily.

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

---

## Install kubectl

`kubectl` is the command-line tool used to communicate with a Kubernetes cluster.

```bash
brew install kubectl
```

Check the installed version:

```bash
kubectl version --client
```

---

## Install Docker Desktop

Docker is required by Minikube to run containers.

Download and install Docker Desktop for Mac:

[Docker Desktop Installation Guide](http://docs.docker.com/desktop/setup/install/mac-install/)

After installation, open Docker Desktop and make sure it is running.

---

# 2. Start Minikube

Start a local Kubernetes cluster:

```bash
minikube start
```

Check the status of Minikube:

```bash
minikube status
```

Open the Kubernetes Dashboard:

```bash
minikube dashboard
```

The dashboard allows you to visually monitor your Kubernetes cluster.

---

# 3. Create an Nginx Deployment

Create a Kubernetes Deployment using the official Nginx image:

```bash
kubectl create deployment my-nginx --image=nginx:latest
```

Check the Deployment:

```bash
kubectl get deployments
```

Check the Pods:

```bash
kubectl get pods
```

Example:

```text
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-xxxxxxxxxx-xxxxx   1/1     Running   0          1m
```

---

# 4. Expose the Application

A Deployment creates Pods, but we need a Service to access the application.

Expose the Nginx Deployment:

```bash
kubectl expose deployment my-nginx --port=80 --type=LoadBalancer
```

Check the available Services:

```bash
kubectl get services
```

Access the Nginx application through Minikube:

```bash
minikube service my-nginx
```

This command opens the Nginx application in your browser.

---

# 5. Common Minikube Commands

## Start Minikube

```bash
minikube start
```

## Check Minikube Status

```bash
minikube status
```

## Open Minikube Dashboard

```bash
minikube dashboard
```

## Delete the Minikube Cluster

```bash
minikube delete
```

> ⚠️ `minikube delete` removes the local Kubernetes cluster and its resources.

---

# 6. Create and Delete a Custom Application

You can deploy your own Docker image to Kubernetes.

Replace `YOUR_IMAGE_NAME` with your Docker image name:

```bash
kubectl create deployment my-app --image=YOUR_IMAGE_NAME
```

For example:

```bash
kubectl create deployment my-app --image=nginx:latest
```

---

## Check Deployments

```bash
kubectl get deployments
```

---

## Check Pods

```bash
kubectl get pods
```

---

## Expose the Application

Expose the Deployment using a LoadBalancer Service:

```bash
kubectl expose deployment my-app --type=LoadBalancer --port=80
```

---

## Check Services

```bash
kubectl get services
```

---

## Access the Application

```bash
minikube service my-app
```

This command opens the application in your browser.

---

## Delete the Deployment

When you no longer need the application:

```bash
kubectl delete deployment my-app
```

You can also delete the Service:

```bash
kubectl delete service my-app
```

---

# Kubernetes Workflow

The basic Kubernetes workflow is:

```text
1. Start Minikube
        ↓
2. Create a Deployment
        ↓
3. Kubernetes creates Pods
        ↓
4. Expose the Deployment using a Service
        ↓
5. Access the application
```

Example:

```bash
minikube start

kubectl create deployment my-nginx --image=nginx:latest

kubectl get deployments

kubectl get pods

kubectl expose deployment my-nginx --port=80 --type=LoadBalancer

kubectl get services

minikube service my-nginx
```

---

# Useful Kubernetes Commands

| Command                          | Description                   |
| -------------------------------- | ----------------------------- |
| `minikube start`                 | Start a Minikube cluster      |
| `minikube status`                | Check Minikube status         |
| `minikube dashboard`             | Open the Kubernetes Dashboard |
| `minikube delete`                | Delete the Minikube cluster   |
| `kubectl get deployments`        | List Deployments              |
| `kubectl get pods`               | List Pods                     |
| `kubectl get services`           | List Services                 |
| `kubectl delete deployment NAME` | Delete a Deployment           |
| `kubectl expose deployment NAME` | Create a Service              |
| `minikube service NAME`          | Open a Service in the browser |

---

# Conclusion

In this project, we learned how to:

* Install Kubernetes tools on macOS
* Install and use `kubectl`
* Install Docker Desktop
* Create a local Kubernetes cluster using Minikube
* Create a Kubernetes Deployment
* Run an Nginx application
* Create a LoadBalancer Service
* Access an application using Minikube
* Check Deployments, Pods, and Services
* Delete Kubernetes resources

This is a basic introduction to running applications on Kubernetes locally.





# Kubernetes


Install kubernets on mac so easy first install brew 


/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

then  brew install kubectl

kubectl version --client

install docker 

http://docs.docker.com/desktop/setup/install/mac-install/

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

kubectl create deployment my-app --image=link
kubectl get deployments
kubectl get pods
kubectl delete deployment my-app

kubectl expose deployment my-app --
type=LoadBalancer --port=80

minikube service my-app

kubectl get services



