# Kubernetes Resource Limits, Requests & LimitRange

## 📌 Overview

Kubernetes allows administrators to control how much CPU and memory resources containers can use.

This is done using:

* **Resource Requests** → Minimum resources required by a container.
* **Resource Limits** → Maximum resources a container can consume.
* **LimitRange** → Namespace-level rules that enforce minimum and maximum resource limits.

These features help prevent one container from consuming all cluster resources.

---

# Kubernetes Resource Management

## CPU and Memory Concepts

### Resource Requests

A request defines the minimum amount of resources required by a container.

Example:

```yaml
resources:
  requests:
    cpu: "0.75"
```

Meaning:

* Kubernetes reserves `0.75 CPU`.
* The scheduler uses this value to find a suitable node.
* The Pod will not be scheduled if enough CPU is unavailable.

---

### Resource Limits

A limit defines the maximum resources a container can use.

Example:

```yaml
resources:
  limits:
    cpu: "1"
```

Meaning:

* Container can use maximum `1 CPU core`.
* If it tries to use more CPU, Kubernetes throttles the CPU usage.

---

# CPU Limit Example

## Create Pod with CPU Limit

Create file:

```bash
vi default-cpu-demo-2.yaml
```

YAML:

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

---

## Explanation

This Pod creates:

```
Pod
 |
 └── Nginx Container
        |
        └── CPU Limit: 1 CPU
```

Configuration:

```yaml
limits:
  cpu: "1"
```

means:

* Container can use maximum 1 CPU core.
* Kubernetes prevents the container from consuming unlimited CPU.
* Extra CPU usage will be throttled.

---

## Apply Pod

```bash
kubectl apply -f default-cpu-demo-2.yaml
```

Check:

```bash
kubectl get pods
```

---

# CPU Request Example

Now we will create a Pod with only CPU request.

Create file:

```bash
vi cpu1.yaml
```

YAML:

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

---

## Explanation

This creates an Nginx container with:

```yaml
requests:
  cpu: "0.75"
```

Meaning:

* Kubernetes reserves 0.75 CPU.
* Scheduler checks node resources before placing the Pod.
* The container is guaranteed this CPU amount.

Unlike limits:

* Requests = guaranteed resources.
* Limits = maximum allowed resources.

---

## Check Pod Details

```bash
kubectl describe pod default-cpu-demo-3
```

Check all pods:

```bash
kubectl get pods
```

---

# LimitRange in Kubernetes

## What is LimitRange?

A LimitRange defines default, minimum, and maximum resource values inside a namespace.

It prevents users from creating containers with:

* Too few resources.
* Too many resources.

---

# Create Memory LimitRange

Create file:

```bash
vi limit-range.yaml
```

YAML:

```yaml
apiVersion: v1
kind: LimitRange

metadata:
  name: mem-min-max-demo-lr

spec:
  limits:
    - max:
        memory: 1Gi

      min:
        memory: 500Mi

      type: Container
```

---

# LimitRange Explanation

This configuration creates:

```
Minimum Memory:
500Mi


Maximum Memory:
1Gi
```

Rules:

| Setting        | Meaning                                     |
| -------------- | ------------------------------------------- |
| min memory     | Every container must request at least 500Mi |
| max memory     | No container can use more than 1Gi          |
| type Container | Rules apply to containers                   |

---

## Apply LimitRange

```bash
kubectl apply -f limit-range.yaml
```

Check:

```bash
kubectl describe limitrange mem-min-max-demo-lr
```

---

# Memory Request and Limit Example

Now we create a container:

Request:

```
600Mi
```

Limit:

```
800Mi
```

This is valid because:

```
Minimum allowed = 500Mi

Request = 600Mi ✅

Maximum allowed = 1Gi

Limit = 800Mi ✅
```

---

## Create Pod

File:

```bash
vi mem.yaml
```

YAML:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: constraints-mem-demo

spec:
  containers:
    - name: constraints-mem-demo-ctr

      image: nginx

      resources:

        limits:
          memory: "800Mi"

        requests:
          memory: "600Mi"
```

---

## Explanation

### Memory Request

```yaml
requests:
  memory: "600Mi"
```

Means:

* Kubernetes reserves 600Mi memory.
* Scheduler finds a node with enough memory.

---

### Memory Limit

```yaml
limits:
  memory: "800Mi"
```

Means:

* Container can use maximum 800Mi memory.
* If memory usage goes above this limit, Kubernetes may terminate the container with:

```
OOMKilled
```

---

## Apply Pod

```bash
kubectl apply -f mem.yaml
```

Check:

```bash
kubectl get pods
```

Describe:

```bash
kubectl describe pod constraints-mem-demo
```

---

# Automatic Request Assignment

Important Kubernetes behavior:

If only a limit is provided:

Example:

```yaml
resources:
  limits:
    memory: "800Mi"
```

Kubernetes automatically creates:

```yaml
requests:
  memory: "800Mi"
```

Because:

```
Request = Limit
```

---

# Testing Invalid Memory Limit

Now we create a Pod exceeding the LimitRange maximum.

LimitRange:

```
Maximum memory = 1Gi
```

Pod configuration:

```yaml
resources:
  limits:
    memory: "1200Mi"

  requests:
    memory: "600Mi"
```

---

## Apply Pod

```bash
kubectl apply -f mem1.yaml
```

---

## Error Output

```text
Error from server (Forbidden):

pods "constraints-mem-demo" is forbidden:

maximum memory usage per Container is 1Gi,
but limit is 1200Mi
```

---

# Why Kubernetes Rejected This Pod?

The LimitRange rule says:

```
Maximum Memory Allowed = 1Gi
```

But the container requested:

```
Memory Limit = 1200Mi
```

Comparison:

```
1200Mi > 1Gi
```

Therefore:

```
Pod Creation Denied ❌
```

---

# Resource Management Flow

```
User Creates Pod
        |
        |
Kubernetes Checks LimitRange
        |
        |
  -------------------------
  |                       |
Valid Resource       Invalid Resource
  |                       |
  |                       |
Pod Created          Pod Rejected
  |
  |
Scheduler Finds Node
  |
  |
Container Starts
```

---

# Useful Commands

## Create Resource Objects

```bash
kubectl apply -f filename.yaml
```

---

## List Pods

```bash
kubectl get pods
```

---

## Describe Pod Resources

```bash
kubectl describe pod pod-name
```

---

## Check LimitRange

```bash
kubectl get limitrange
```

---

```bash
kubectl describe limitrange limitrange-name
```

---

# Summary

In this tutorial:

✅ Created CPU limits
✅ Created CPU requests
✅ Learned difference between request and limit
✅ Created Kubernetes LimitRange
✅ Applied minimum and maximum memory rules
✅ Created valid memory resource configuration
✅ Tested invalid memory configuration
✅ Observed Kubernetes rejecting resources above the allowed limit

Resource management helps Kubernetes maintain stability and prevents containers from consuming excessive cluster resources.















============================================

Now we will be using the limit in Kubernetes 

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

# Explanation:
# This Pod creates an Nginx container named default-cpu-demo-2-ctr.
# The resources section defines the CPU limit for the container.
# cpu: "1" means the container can use a maximum of 1 CPU core.
# If the container tries to use more than 1 CPU, Kubernetes will restrict (throttle) the CPU usage.
# This helps control resource consumption and prevents one container from using all available CPU resources.
# You will also see the request is 1 also by default


=========================================================================================================

Now after that 

this time I am not mentioning the limit  will take cpu default limit will pickup and the limit is one 


vi cpu1.yaml


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

# Explanation:
# This Pod creates an Nginx container named default-cpu-demo-3-ctr.
# The resources section defines the minimum CPU required by the container.
# cpu: "0.75" means Kubernetes will reserve 0.75 CPU core for this container.
# Kubernetes scheduler uses this request value to decide which node has enough CPU resources.
# Unlike limits, requests guarantee CPU availability for the container.
# If a node does not have 0.75 CPU available, Kubernetes will not schedule this Pod on that node.


kubectl describe pod default-cpu-demo-3 

kubectl get pods
===============================================================


I have created the limit range 500 minus and maximum 1gb

now lets create the limit range 


vi limit range.yaml

apiVersion: v1

kind: LimitRange

metadata:
  name: mem-min-max-demo-lr

spec:
  limits:
    - max:
        memory: 1Gi

      min:
        memory: 500Mi

      type: Container


# Explanation:
# This LimitRange sets the minimum and maximum memory allowed for containers in a namespace.
#
# min:
# memory: 500Mi means every container must request at least 500Mi memory.
#
# max:
# memory: 1Gi means no container can use more than 1Gi memory.
#
# type: Container means these restrictions are applied to containers.
#
# Any Pod created in this namespace must follow these memory rules.


========================

now I am creating the container   memory: "800Mi" maximum memory: "600Mi"
it will run as you can see also 


Limits:
      cpu:     1
      memory:  800Mi // maximum
    Requests:
      cpu:        500m
      memory:     600Mi
    Environment:  <none>

create the new pod now 


==============================

vi mem.yaml

apiVersion: v1

kind: Pod

metadata:
  name: constraints-mem-demo

spec:
  containers:
    - name: constraints-mem-demo-ctr

      image: nginx

      resources:
        limits:
          memory: "800Mi"

        requests:
          memory: "600Mi"


# Explanation:
# This Pod creates an Nginx container with memory requirements.
#
# requests:
# memory: "600Mi" means Kubernetes reserves 600Mi memory for this container.
# The scheduler uses this value to find a suitable node.
#
# limits:
# memory: "800Mi" means the container can use a maximum of 800Mi memory.
# If the container tries to use more than 800Mi, Kubernetes may terminate the container (OOMKilled).
#
# According to the LimitRange:
# Minimum allowed memory = 500Mi
# Maximum allowed memory = 1Gi
#
# This Pod is valid because:
# Request (600Mi) >= Minimum (500Mi)
# Limit (800Mi) <= Maximum (1Gi)
#
# Important:
# If a container has only a memory limit and no request,
# Kubernetes automatically sets the request value equal to the limit value.
#
# Example:
# resources:
#   limits:
#     memory: "800Mi"
#
# Kubernetes automatically applies:
# requests:
#   memory: "800Mi"

===============================================

# Now I will be giving more than the limit and lets check what will happen  limits:
  memory: "1200Mi"

   ======================================       
apiVersion: v1

kind: Pod

metadata:
  name: constraints-mem-demo

spec:
  containers:
    - name: constraints-mem-demo-ctr

      image: nginx

      resources:
        limits:
          memory: "1200Mi"

        requests:
          memory: "600Mi"

          
output: Error from server (Forbidden): error when creating "mem1.yaml": pods "constraints-mem-demo" is forbidden: maximum memory usage per Container is 1Gi, but limit is 1800Mi
