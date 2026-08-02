# Kubernetes Namespace, Metrics Server & Resource Management

## Overview

This project demonstrates basic Kubernetes concepts:

* Kubernetes Namespaces
* Creating and managing Pods
* Deploying applications using YAML files
* Metrics Server installation
* Checking CPU and Memory usage
* Understanding Resource Requests and Limits

---

# 1. Kubernetes Namespace

## What is a Namespace?

A Namespace is a logical separation inside a Kubernetes cluster.

It helps organize and manage resources such as:

* Pods
* Services
* Deployments
* ConfigMaps
* Secrets

Example Kubernetes structure:

```
Kubernetes Cluster

├── default
├── kube-system
└── demo
```

---

# 2. Check Available Namespaces

To view all namespaces:

```bash
kubectl get namespace
```

Example:

```
NAME              STATUS
default           Active
kube-system       Active
kube-public       Active
```

---

# 3. Check Kubernetes System Pods

The `kube-system` namespace contains Kubernetes internal components.

Examples:

* CoreDNS
* kube-proxy
* Metrics Server
* Controller Manager

Command:

```bash
kubectl get pods -n kube-system
```

Example output:

```
NAME              READY     STATUS
coredns           1/1       Running
kube-proxy        1/1       Running
```

---

# 4. Create a New Namespace

Create a namespace named `demo`:

```bash
kubectl create ns demo
```

Output:

```
namespace/demo created
```

Verify:

```bash
kubectl get ns
```

Now you will see:

```
default
kube-system
demo
```

---

# 5. Create Kubernetes Pod YAML

Create a YAML file:

```bash
vi pod.yaml
```

Add:

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx
  namespace: demo

spec:
  containers:
  - name: nginx
    image: nginx:1.14.2

    ports:
    - containerPort: 80
```

---

# YAML Explanation

## apiVersion

Defines the Kubernetes API version.

```yaml
apiVersion: v1
```

---

## kind

Defines the Kubernetes object type.

```yaml
kind: Pod
```

---

## metadata

Contains information about the Pod.

```yaml
metadata:
  name: nginx
  namespace: demo
```

Here:

* Pod name = nginx
* Namespace = demo

---

## containers

Defines the application running inside the Pod.

```yaml
containers:
- name: nginx
  image: nginx:1.14.2
```

This creates an Nginx container.

---

# 6. Deploy Pod

Apply YAML configuration:

```bash
kubectl apply -f pod.yaml
```

Output:

```
pod/nginx created
```

---

# 7. Check Pods

Check default namespace:

```bash
kubectl get pods
```

The nginx Pod will not appear because it is inside the `demo` namespace.

Check demo namespace:

```bash
kubectl get pods -n demo
```

Output:

```
NAME      READY     STATUS
nginx     1/1       Running
```

---

# 8. Metrics Server

## What is Metrics Server?

Metrics Server collects resource usage information from Kubernetes.

It provides:

* CPU usage
* Memory usage

Install Metrics Server:

```bash
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Check Metrics Server:

```bash
kubectl get pods -n kube-system
```

---

# 9. Check Node Resource Usage

Command:

```bash
kubectl top nodes
```

Example:

```
NAME       CPU(cores)    MEMORY(bytes)

minikube   250m          1024Mi
```

Explanation:

```
250m CPU = 0.25 CPU Core

1024Mi Memory = 1GB RAM
```

---

# 10. Kubernetes Resource Requests and Limits

Kubernetes allows us to control how much CPU and Memory a container can use.

Example:

```yaml
resources:

  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"
```

---

# Resource Requests

## What are Requests?

Requests define the minimum resources required by a container.

Example:

```yaml
requests:
  cpu: "100m"
  memory: "128Mi"
```

Meaning:

```
CPU    = 100m = 0.1 CPU Core

Memory = 128Mi RAM
```

Kubernetes uses requests during Pod scheduling.

Example:

```
Node Available:

CPU: 500m

Pod Requirement:

CPU: 100m

Pod can be scheduled ✅
```

---

# Resource Limits

## What are Limits?

Limits define the maximum resources a container can consume.

Example:

```yaml
limits:
  cpu: "500m"
  memory: "512Mi"
```

Meaning:

```
Maximum CPU:

500m = 0.5 CPU Core


Maximum Memory:

512Mi RAM
```

If a container exceeds the limit:

* CPU may be throttled
* Memory may cause OOMKilled error

---

# 11. Complete Pod YAML With Resources

```yaml
apiVersion: v1

kind: Pod

metadata:
  name: nginx
  namespace: demo


spec:

  containers:

  - name: nginx

    image: nginx:1.14.2


    ports:
    - containerPort: 80


    resources:

      requests:
        cpu: "100m"
        memory: "128Mi"


      limits:
        cpu: "500m"
        memory: "512Mi"
```

---

# Requests vs Limits

| Type     | Purpose                   |
| -------- | ------------------------- |
| Requests | Minimum resources needed  |
| Limits   | Maximum resources allowed |

Example:

```
Requests:

CPU      100m
Memory   128Mi


Limits:

CPU      500m
Memory   512Mi
```

---

# Useful Kubernetes Commands

## Get Namespaces

```bash
kubectl get ns
```

---

## Get All Pods

```bash
kubectl get pods
```

---

## Get Pods From Specific Namespace

```bash
kubectl get pods -n demo
```

---

## Describe Pod

```bash
kubectl describe pod nginx -n demo
```

---

## Check Node Usage

```bash
kubectl top nodes
```

---

## Delete Namespace

```bash
kubectl delete ns demo
```

---

# Kubernetes Learning Flow

```
Create Namespace
        |
        ↓
Create YAML File
        |
        ↓
Deploy Pod
        |
        ↓
Check Pod Status
        |
        ↓
Install Metrics Server
        |
        ↓
Monitor CPU & Memory
        |
        ↓
Apply Resource Requests & Limits
```

---

# Summary

* Namespace is used to organize Kubernetes resources.
* Pods can run inside specific namespaces.
* YAML files describe Kubernetes resources.
* Metrics Server helps monitor resource usage.
* Requests define minimum required resources.
* Limits define maximum allowed resources.
* Kubernetes uses requests when scheduling Pods.

## Author

Mudasir ahmad /@anberlin









# 
kubectl get namespace

kubectl get pods -n kube-system

kubectl create ns demo

create new namespace  demo 

vi pod.yaml

kubectl apply -f pod.yaml

kubectl get pods

 kubectl get pods -n demo

 
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml




kubectl top nodes


In Kubernetes, requests and limits are used to control how much CPU and memory a container gets.

apiVersion: apps/v1



resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"


   Requests = Minimum needed

A request tells Kubernetes:

“My container needs at least this much resource.”

requests:
  cpu: "100m"
  memory: "128Mi"


  

  This means Kubernetes will try to find a Node with at least:

100m CPU = 0.1 CPU core
128Mi memory

Kubernetes uses requests when scheduling Pods.



Limits = Maximum allowed

A limit tells Kubernetes:

“My container cannot use more than this amount.”

limits:
  cpu: "500m"
  memory: "512Mi"


  


The container can use up to:

500m CPU = 0.5 CPU core
512Mi memory
