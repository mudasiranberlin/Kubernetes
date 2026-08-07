# Kubernetes Metrics Server + Horizontal Pod Autoscaler (HPA)

This guide explains how to install Metrics Server and test Kubernetes Horizontal Pod Autoscaling (HPA).

---

# 1. Install Metrics Server

Metrics Server collects CPU and memory usage metrics from Kubernetes nodes and Pods.

It is required for:

- `kubectl top nodes`
- `kubectl top pods`
- Horizontal Pod Autoscaler (HPA)

---

## Install on Linux / Ubuntu

### Download Metrics Server YAML file

```bash
wget -O metricsserver.yml https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

You can also download from:

```
https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

---

### Apply Metrics Server

```bash
kubectl apply -f metricsserver.yml
```

Check Metrics Server Pod:

```bash
kubectl get pods -n kube-system
```

---

# Install on Mac Terminal

macOS does not include `wget` by default.

Install wget using Homebrew:

```bash
brew install wget
```

Download Metrics Server:

```bash
wget -O metricsserver.yml https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```

Check file:

```bash
ls
```

You should see:

```
metricsserver.yml
```

---

# Configure Metrics Server for Minikube

Sometimes Metrics Server fails because of kubelet certificate issues.

Edit the YAML file:

```bash
vi metricsserver.yml
```

Find:

```yaml
containers:
- args:
```

Add:

```yaml
- --kubelet-insecure-tls
```

Example:

```yaml
containers:
- args:
  - --cert-dir=/tmp
  - --secure-port=10250
  - --kubelet-preferred-address-types=InternalIP,Hostname
  - --kubelet-insecure-tls
```

Save and apply again:

```bash
kubectl apply -f metricsserver.yml
```

---

# Check Metrics Server Status

Check namespaces:

```bash
kubectl get namespace
```

Check system Pods:

```bash
kubectl get pods -n kube-system
```

Example:

```
metrics-server-79bf5b7745-f5vl4
```

Check logs:

```bash
kubectl logs -f metrics-server-79bf5b7745-f5vl4 -n kube-system
```

If everything is working, Metrics Server is ready.

---

# If Metrics Server Has Errors

Delete Metrics Server:

```bash
kubectl delete deployment metrics-server -n kube-system
```

Install again:

```bash
kubectl apply -f metricsserver.yml
```

---

# 2. Create Deployment for HPA Testing

Create file:

```bash
vi deployhpa.yaml
```

Add:

```yaml
apiVersion: apps/v1

kind: Deployment

metadata:
  name: mydeploy

spec:
  replicas: 1

  selector:
    matchLabels:
      name: deployment

  template:
    metadata:
      name: testpod8
      labels:
        name: deployment

    spec:
      containers:
        - name: c00

          image: httpd

          ports:
            - containerPort: 80

          resources:
            limits:
              cpu: 500m

            requests:
              cpu: 200m
```

---

# Deployment Explanation

This Deployment creates and manages Pods running an Apache HTTP Server (`httpd`).

### replicas

```yaml
replicas: 1
```

Kubernetes maintains one running Pod.

---

### Selector

```yaml
matchLabels:
  name: deployment
```

Connects the Deployment with Pods having:

```
name: deployment
```

---

### Container

```yaml
image: httpd
```

Creates a container using the Apache HTTP Server image.

---

### Port

```yaml
containerPort: 80
```

Apache runs on port 80 inside the container.

---

### CPU Request

```yaml
requests:
  cpu: 200m
```

Kubernetes reserves:

```
200m = 0.2 CPU
```

The scheduler uses this value to select a node.

---

### CPU Limit

```yaml
limits:
  cpu: 500m
```

Maximum CPU usage:

```
500m = 0.5 CPU
```

If the container tries to use more CPU, Kubernetes throttles it.

---

## CPU Values

```
1000m = 1 CPU Core

500m  = 0.5 CPU Core

200m  = 0.2 CPU Core
```

---

# Create Deployment

Apply:

```bash
kubectl apply -f deployhpa.yaml
```

Check resources:

```bash
kubectl get all
```

---

# 3. Create Horizontal Pod Autoscaler (HPA)

HPA automatically increases or decreases Pods depending on CPU usage.

Run:

```bash
kubectl autoscale deployment mydeploy --cpu-percent=20 --min=1 --max=10
```

New format:

```bash
kubectl autoscale deployment mydeploy --cpu=20% --min=1 --max=10
```

---

# Check HPA

```bash
kubectl get hpa
```

Example:

```
NAME       REFERENCE             TARGETS
mydeploy   Deployment/mydeploy   0%/20%
```

Currently:

```
1 Pod running
```

---

# 4. Test Auto Scaling

Open another terminal window.

## Terminal 1: Watch scaling

Linux:

```bash
watch kubectl get all
```

Mac:

Install watch:

```bash
brew install watch
```

Then:

```bash
watch kubectl get all
```

---

## Terminal 2: Generate CPU Load

Run:

```bash
apt update
```

Run repeatedly to increase traffic/load.

When CPU goes above 20%:

```
1 Pod
   |
   v
2 Pods
   |
   v
3 Pods
   |
   .
   .
   .
10 Pods
```

HPA automatically creates more Pods.

---

# Wait for Scale Down

After traffic stops, Kubernetes waits and then removes extra Pods.

After around 10 minutes:

```
10 Pods
   |
   v
1 Pod
```

The minimum replicas remain:

```
min=1
```

---

# Useful Commands

Check Pods:

```bash
kubectl get pods
```

Check HPA:

```bash
kubectl get hpa
```

Check CPU usage:

```bash
kubectl top pods
```

Check node resources:

```bash
kubectl top nodes
```


=================================================
# install Metrics Server

install on the linux or ubuntu 

Download the Metrics Server YAML file

wget -O metricsserver.yml https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml


file also here 
https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

kubectl apply -f metricsserver.yml

kubectl get pods -n kube-system


# Mac Terminal,

wget -O metricsserver.yml https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml

macOS does not include wget by default. Install it using Homebrew:


brew install wget

run again 


wget -O metricsserver.yml https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml



You should see:

metricsserver.yml

# Apply Metrics Server to Kubernetes:

kubectl apply -f metricsserver.yml



# Check Metrics Server status:

kubectl get pods -n kube-system | grep metrics

you may need to edit the YAML first and add:

- --kubelet-insecure-tls

vi metricsserver.yml


paste this in  - --kubelet-insecure-tls


here u will see : 
containers:
  - args:
      - --cert-dir=/tmp
      - --secure-port=10250
      - --kubelet-preferred-address-types=InternalIP,Hostname
      - --kubelet-insecure-tls

kubectl apply -f metricsserver.yml


kubectl get namespace

kubectl get pods -n kube-system

this one name of that (metrics-server-79bf5b7745-f5vl4 -n kube-system)

kubectl logs -f metrics-server-79bf5b7745-f5vl4 -n kube-system


Everything is fine delete the    metrics-server and install again 




if you got any error 


now deploy a file 

vi deployhpa.yaml

======================

apiVersion: apps/v1

kind: Deployment

metadata:
  name: mydeploy

spec:
  replicas: 1

  selector:
    matchLabels:
      name: deployment

  template:
    metadata:
      name: testpod8
      labels:
        name: deployment

    spec:
      containers:
        - name: c00

          image: httpd

          ports:
            - containerPort: 80

          resources:
            limits:
              cpu: 500m

            requests:
              cpu: 200m


# Explanation:
# This Deployment creates and manages Pods running an Apache HTTP Server (httpd).
#
# replicas: 1 means Kubernetes will maintain one Pod running at all times.
#
# selector.matchLabels connects the Deployment with Pods having the label:
# name: deployment
#
# template defines the Pod configuration that the Deployment will create.
#
# The container name is c00 and it uses the httpd Docker image.
#
# containerPort: 80 exposes port 80 inside the container because Apache runs on port 80.
#
# resources.requests.cpu: 200m means Kubernetes reserves 200 millicores (0.2 CPU)
# for this container. The scheduler uses this value to find a suitable node.
#
# resources.limits.cpu: 500m means the container can use a maximum of 500 millicores
# (0.5 CPU). If it tries to use more CPU, Kubernetes will throttle the container.
#
# CPU values:
# 1000m = 1 CPU core
# 500m  = 0.5 CPU core
# 200m  = 0.2 CPU core




===============

we create this pod to check how the auto scaling happen 

kubectl apply -f deployhpa.yml

kubectl get all 

check the container u created and other details 

now I will apply horizontal scaling and you check the difference 


# creates a Horizontal Pod Autoscaler (HPA) for your Deployment.

kubectl autoscale deployment mydeploy --cpu-percent=20 --min=1 --max=10

or 

New format 

kubectl autoscale deployment mydeploy --cpu=20% --min=1 --max=10

now its autoscaled mydeploy

kubectl get all 

kubectl get hpa


only 1 pod you see now 

now I will increase the traffic I will open new window on my Mac terminal 
or in ec2 again open the machine in new browser 

first tab we will run the command to watch how it will scale it :


watch kubectl get all

if error install : 

brew install watch

second window run 

apt update


again and again so it will create the new pods 

and wait 10 minutes it will delete the pods 


 
