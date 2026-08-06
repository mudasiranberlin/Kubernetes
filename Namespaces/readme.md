# Kubernetes Namespaces, Resource Requests, Resource Limits, ResourceQuota & LimitRange

This project demonstrates how to use **Namespaces**, **Pods**, **CPU & Memory Requests/Limits**, **ResourceQuota**, **Deployments**, and **LimitRange** in Kubernetes.

---

# Prerequisites

- Kubernetes Cluster (Minikube, Kind, or any Kubernetes Cluster)
- kubectl installed
- Running Kubernetes Cluster

Verify your cluster:

```bash
kubectl cluster-info
kubectl get nodes
```

---

# Step 1: Create a Namespace

Create a file named **namespace.yml**

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: dev
  labels:
    name: dev
```

Apply the namespace:

```bash
kubectl apply -f namespace.yml
```

Verify:

```bash
kubectl get namespaces
```

Expected Output

```text
NAME              STATUS   AGE
default           Active
dev               Active
kube-system       Active
```

---

# Step 2: Switch to the dev Namespace

Instead of writing `-n dev` with every command, change the current namespace.

```bash
kubectl config set-context $(kubectl config current-context) --namespace=dev
```

Verify the current namespace:

```bash
kubectl config view | grep namespace:
```

Output

```text
namespace: dev
```

---

# Step 3: Create a Pod

Create a file named **pod.yml**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: testpod
spec:
  containers:
  - name: c00
    image: ubuntu
    command:
    - /bin/bash
    - -c
    - while true; do echo Technical Guftgu; sleep 5; done
  restartPolicy: Never
```

Create the Pod:

```bash
kubectl apply -f pod.yml
```

Verify:

```bash
kubectl get pods
```

Describe Pod:

```bash
kubectl describe pod testpod
```

View Logs:

```bash
kubectl logs testpod
```

Delete Pod:

```bash
kubectl delete pod testpod
```

---

# Step 4: Create a Pod with CPU & Memory Requests/Limits

Create a file named **resource-pod.yml**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: resources
spec:
  containers:
  - name: resource
    image: centos
    command:
    - /bin/bash
    - -c
    - while true; do echo Technical-Guftgu; sleep 5; done
    resources:
      requests:
        memory: "64Mi"
        cpu: "100m"
      limits:
        memory: "128Mi"
        cpu: "200m"
```

Create the Pod:

```bash
kubectl apply -f resource-pod.yml
```

Verify:

```bash
kubectl get pods
```

Describe:

```bash
kubectl describe pod resources
```

View Resource Allocation:

```bash
kubectl top pod resources
```

> **Note:** Metrics Server must be installed for `kubectl top` to work.

Delete:

```bash
kubectl delete pod resources
```

---

# Understanding Resource Requests

Requests define the minimum resources guaranteed to the container.

```yaml
requests:
  cpu: "100m"
  memory: "64Mi"
```

Meaning:

- CPU = 100 millicores (0.1 CPU)
- Memory = 64 MiB

---

# Understanding Resource Limits

Limits define the maximum resources a container can consume.

```yaml
limits:
  cpu: "200m"
  memory: "128Mi"
```

Meaning:

- Maximum CPU = 200 millicores
- Maximum Memory = 128 MiB

---

# Step 5: Create a ResourceQuota

Create **resourcequota.yml**

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: myquota
spec:
  hard:
    limits.cpu: "400m"
    limits.memory: "400Mi"
    requests.cpu: "200m"
    requests.memory: "200Mi"
```

Create ResourceQuota:

```bash
kubectl apply -f resourcequota.yml
```

Verify:

```bash
kubectl get resourcequota
```

Describe:

```bash
kubectl describe resourcequota myquota
```

Delete:

```bash
kubectl delete resourcequota myquota
```

---

# What is ResourceQuota?

A **ResourceQuota** limits the total amount of CPU and Memory that all Pods inside a namespace can consume.

For this example:

| Resource | Maximum |
|----------|----------|
| CPU Requests | 200m |
| Memory Requests | 200Mi |
| CPU Limits | 400m |
| Memory Limits | 400Mi |

If the namespace reaches these limits, Kubernetes will reject new Pods that exceed the quota.

---

# Step 6: Create a Deployment

Create **deployment.yml**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployments
spec:
  replicas: 3
  selector:
    matchLabels:
      objtype: deployment
  template:
    metadata:
      labels:
        objtype: deployment
    spec:
      containers:
      - name: c00
        image: ubuntu
        command:
        - /bin/bash
        - -c
        - while true; do echo Technical-Guftgu; sleep 5; done
        resources:
          requests:
            cpu: "200m"
```

Create Deployment:

```bash
kubectl apply -f deployment.yml
```

Verify:

```bash
kubectl get deployments
kubectl get rs
kubectl get pods
```

Describe:

```bash
kubectl describe deployment deployments
```

Scale Deployment:

```bash
kubectl scale deployment deployments --replicas=5
```

Delete:

```bash
kubectl delete deployment deployments
```

---

# Step 7: Create a LimitRange

Create **limitrange.yml**

```yaml
apiVersion: v1
kind: LimitRange
metadata:
  name: cpu-limit-range
spec:
  limits:
  - default:
      cpu: "1"
    defaultRequest:
      cpu: "0.5"
    type: Container
```

Apply:

```bash
kubectl apply -f limitrange.yml
```

Verify:

```bash
kubectl get limitrange
```

Describe:

```bash
kubectl describe limitrange cpu-limit-range
```

Delete:

```bash
kubectl delete limitrange cpu-limit-range
```

---

# What is LimitRange?

A **LimitRange** automatically assigns default CPU and Memory values to containers that do not specify them.

Example:

```yaml
default:
  cpu: "1"

defaultRequest:
  cpu: "0.5"
```

Meaning:

- Default CPU Request = 0.5 CPU
- Default CPU Limit = 1 CPU

---

# Step 8: Create CPU Limit Example

Create **cpu2.yml**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: default-cpu-demo-2
spec:
  containers:
  - name: default-cpu-demo-2-ctr
    image: nginx
    resources:
      limits:
        cpu: "1"
```

Apply:

```bash
kubectl apply -f cpu2.yml
```

Verify:

```bash
kubectl get pods
```

Describe:

```bash
kubectl describe pod default-cpu-demo-2
```

Delete:

```bash
kubectl delete pod default-cpu-demo-2
```

---

# Step 9: Create CPU Request Example

Create **cpu3.yml**

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: default-cpu-demo-3
spec:
  containers:
  - name: default-cpu-demo-3-ctr
    image: nginx
    resources:
      requests:
        cpu: "0.75"
```

Apply:

```bash
kubectl apply -f cpu3.yml
```

Verify:

```bash
kubectl get pods
```

Describe:

```bash
kubectl describe pod default-cpu-demo-3
```

Delete:

```bash
kubectl delete pod default-cpu-demo-3
```

---

# Useful kubectl Commands

### Namespaces

```bash
kubectl get namespaces
kubectl describe namespace dev
kubectl delete namespace dev
```

### Pods

```bash
kubectl get pods
kubectl get pods -o wide
kubectl describe pod testpod
kubectl logs testpod
kubectl delete pod testpod
```

### Deployments

```bash
kubectl get deployments
kubectl describe deployment deployments
kubectl scale deployment deployments --replicas=5
kubectl delete deployment deployments
```

### ResourceQuota

```bash
kubectl get resourcequota
kubectl describe resourcequota myquota
kubectl delete resourcequota myquota
```

### LimitRange

```bash
kubectl get limitrange
kubectl describe limitrange cpu-limit-range
kubectl delete limitrange cpu-limit-range
```

### Resource Usage

```bash
kubectl top nodes
kubectl top pods
```

### Current Namespace

```bash
kubectl config view --minify | grep namespace
```

Switch back to the default namespace:

```bash
kubectl config set-context $(kubectl config current-context) --namespace=default
```

---

# Cleanup

Delete all resources:

```bash
kubectl delete -f cpu3.yml
kubectl delete -f cpu2.yml
kubectl delete -f deployment.yml
kubectl delete -f resource-pod.yml
kubectl delete -f pod.yml
kubectl delete -f resourcequota.yml
kubectl delete -f limitrange.yml
kubectl delete -f namespace.yml
```

---

# Concepts Covered

- Kubernetes Namespace
- Pods
- Resource Requests
- Resource Limits
- ResourceQuota
- Deployments
- LimitRange
- CPU Requests
- CPU Limits
- Namespace Context
- Resource Management

---

# Learning Outcomes

After completing this project, you will be able to:

- Create Kubernetes namespaces.
- Switch the active namespace.
- Create Pods.
- Configure CPU and Memory requests.
- Configure CPU and Memory limits.
- Create ResourceQuota.
- Create LimitRange.
- Deploy applications using Deployments.
- Scale Deployments.
- Monitor resource usage.
- Clean up Kubernetes resources.








kubectl get namespace

vi devns.yaml

apiVersion: v1
kind: Namespace

metadata:
  name: dev
  labels:
    name: dev

===============================

kubectl apply -f devns.yaml

kubectl get namespace


===================
vi pod17.yaml

===================

apiVersion: v1
kind: Pod

metadata:
  name: testpod

spec:
  containers:
  - name: c00
    image: ubuntu
    command: ["/bin/bash", "-c", "while true; do echo Technical Guftgu; sleep 5; done"]

===============================

kubectl apply -f pod17.yaml -n dev

kubectl get pods -n dev

kubectl delete -f  pod17.yaml -n dev

change kubectl get pods (default to dev)

kubectl config set-context $(kubectl config current-context) --namespace=dev


change back to default 

kubectl config set-context $(kubectl config current-context) --namespace=default

check namespace


kubectl config view | grep namespace:

=======================================================================================

create limit and request 


==================

vi podresource

apiVersion: v1
kind: Pod

metadata:
  name: resources

spec:
  containers:
  - name: resource
    image: centos
    command: ["/bin/bash", "-c", "while true; do echo Technical-Guftgu; sleep 5; done"]

    resources:
      requests:
        memory: "64Mi"
        cpu: "100m"

      limits:
        memory: "128Mi"
        cpu: "200m"

==========================================

kubectl apply -f podresource.yaml



=======================================


resource quota 


vi resoucequota 


apiVersion: v1
kind: ResourceQuota

metadata:
  name: myquota

spec:
  hard:
    limits.cpu: "400m"
    limits.memory: "400Mi"
    requests.cpu: "200m"
    requests.memory: "200Mi"


===============================


kubectl apply -f resourcequta.yaml



=============================

create new file pod18



apiVersion: apps/v1
kind: Deployment

metadata:
  name: deployments

spec:
  replicas: 3

  selector:
    matchLabels:
      objtype: deployment

  template:
    metadata:
      name: testpod8
      labels:
        objtype: deployment

    spec:
      containers:
      - name: c00
        image: ubuntu
        command: ["/bin/bash", "-c", "while true; do echo Technical-Guftgu; sleep 5; done"]

        resources:
          requests:
            cpu: "200m"

===========================================


When we run this it will show error as the we have write in the  vi resoucequota   we will limit   limits.cpu: "400m"
    limits.memory: "400Mi" 
so will be showing the error 

now I will delete the resource quota 
 kubectl delete pod resources testpod

======================================



 vi cpufault.yaml



apiVersion: v1
kind: LimitRange

metadata:
  name: cpu-limit-range

spec:
  limits:
  - type: Container
    default:
      cpu: "1"
    defaultRequest:
      cpu: "0.5"



      kubectl apply -f cpufault.yaml


    
