# Kubernetes Liveness Probe Example

## 📌 Overview

This project demonstrates how to use a **Kubernetes Liveness Probe** to automatically detect when a container is unhealthy and restart it.

A liveness probe helps Kubernetes check whether an application inside a container is still running correctly.

If the liveness probe fails, Kubernetes will automatically restart the container.

---

# 📚 What is a Liveness Probe?

A **Liveness Probe** is a Kubernetes health check mechanism.

Kubernetes continuously checks the container using the configured probe.

If the check returns a failure:

1. Kubernetes marks the container as unhealthy.
2. The kubelet stops the container.
3. Kubernetes restarts the container automatically.

Common types of liveness probes:

* **exec probe** → Runs a command inside the container.
* **HTTP probe** → Checks an HTTP endpoint.
* **TCP probe** → Checks if a TCP port is open.

In this example, we use an **exec liveness probe**.

---

# 🏗 Architecture

```
                Kubernetes Cluster

                       |
                       |
                   Pod
                       |
              ----------------
              |              |
          Container       Liveness
          Ubuntu          Probe
              |              |
              |              |
       /tmp/healthy     cat /tmp/healthy
```

The probe checks:

```
cat /tmp/healthy
```

If the file exists:

```
Exit code: 0
```

Container is healthy.

If the file does not exist:

```
Exit code: 1
```

Container is unhealthy and Kubernetes restarts it.

---

# 📂 Create Liveness Probe YAML File

Create a file:

```bash
vi mylivenessprobe.yaml
```

---

# Kubernetes Pod YAML

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: mylivenessprobe
  labels:
    test: liveness

spec:
  containers:
    - name: liveness
      image: ubuntu

      args:
        - /bin/sh
        - -c
        - touch /tmp/healthy; sleep 1000

      livenessProbe:
        exec:
          command:
            - cat
            - /tmp/healthy

        initialDelaySeconds: 5
        periodSeconds: 5
        timeoutSeconds: 30
```

---

# YAML Explanation

## Pod Definition

```yaml
apiVersion: v1
```

Uses Kubernetes API version.

---

```yaml
kind: Pod
```

Creates a Pod object.

---

## Metadata

```yaml
metadata:
  name: mylivenessprobe
```

Creates a pod with the name:

```
mylivenessprobe
```

---

## Container

```yaml
containers:
- name: liveness
  image: ubuntu
```

Creates an Ubuntu container.

---

## Container Command

```yaml
args:
- /bin/sh
- -c
- touch /tmp/healthy; sleep 1000
```

When the container starts:

1. Creates a file:

```
/tmp/healthy
```

2. Keeps the container running:

```
sleep 1000
```

---

# Liveness Probe Configuration

```yaml
livenessProbe:
  exec:
    command:
      - cat
      - /tmp/healthy
```

Kubernetes runs:

```bash
cat /tmp/healthy
```

inside the container.

---

## Probe Timing

### initialDelaySeconds

```yaml
initialDelaySeconds: 5
```

Kubernetes waits 5 seconds after container startup before checking.

---

### periodSeconds

```yaml
periodSeconds: 5
```

Probe runs every 5 seconds.

---

### timeoutSeconds

```yaml
timeoutSeconds: 30
```

Probe must complete within 30 seconds.

---

# Deploy the Pod

Create the pod:

```bash
kubectl apply -f mylivenessprobe.yaml
```

Check pod status:

```bash
kubectl get pods
```

Expected output:

```
NAME                READY   STATUS
mylivenessprobe     1/1     Running
```

---

# Test Liveness Probe

Enter inside the container:

```bash
kubectl exec mylivenessprobe -it -- /bin/bash
```

---

Check healthy file:

```bash
cat /tmp/healthy
```

Output:

```
(empty)
```

Check exit code:

```bash
echo $?
```

Output:

```
0
```

Exit code `0` means success.

The container is healthy.

---

# Test Failed Health Check

Run:

```bash
cat /tmp/bupinder
```

Output:

```
cat: /tmp/bupinder: No such file or directory
```

Check exit code:

```bash
echo $?
```

Output:

```
1
```

Exit code `1` means failure.

---

# Simulate Container Failure

Remove the health check file:

Enter container:

```bash
kubectl exec mylivenessprobe -it -- /bin/bash
```

Remove file:

```bash
rm /tmp/healthy
```

Now the liveness probe will fail.

---

# Check Pod Status

Run:

```bash
kubectl get pods
```

You will see Kubernetes restarting the container.

Example:

```
NAME                READY   STATUS
mylivenessprobe     1/1     Running
```

The pod stays running, but the container has been restarted.

---

# View Pod Events

Command:

```bash
kubectl describe pod mylivenessprobe
```

Example output:

```
Events:

Normal   Scheduled
Successfully assigned default/mylivenessprobe

Normal   Pulled
Successfully pulled image ubuntu

Warning  Unhealthy
Liveness probe failed:
cat: /tmp/healthy: No such file or directory

Normal   Killing
Container liveness failed liveness probe,
will be restarted

Normal   Started
Container started
```

---

# How Kubernetes Handles Failure

Flow:

```
Container Starts
       |
       |
Creates /tmp/healthy
       |
       |
Liveness Probe Checks File
       |
       |
       +----------------+
       |                |
       |                |
 File Exists       File Missing
       |                |
       |                |
 Healthy          Probe Failed
                        |
                        |
              Kubernetes Restarts
                 Container
```

---

# Useful Commands

## Check Pods

```bash
kubectl get pods
```

---

## Check Pod Details

```bash
kubectl describe pod mylivenessprobe
```

---

## View Container Logs

```bash
kubectl logs mylivenessprobe
```

---

## Enter Container

```bash
kubectl exec mylivenessprobe -it -- /bin/bash
```

---

## Delete Pod

```bash
kubectl delete pod mylivenessprobe
```

---

# Difference Between Liveness and Readiness Probe

| Probe           | Purpose                                                      |
| --------------- | ------------------------------------------------------------ |
| Liveness Probe  | Checks if application is alive. Restarts container if failed |
| Readiness Probe | Checks if application is ready to receive traffic            |
| Startup Probe   | Checks slow-starting applications                            |

Example:

* Liveness → "Is my application running?"
* Readiness → "Can my application receive users?"
* Startup → "Has my application started successfully?"

---

# Real World Example

For a web application:

```
User
 |
 |
Load Balancer
 |
 |
Kubernetes Service
 |
 |
Pod
 |
 |
Application Container
 |
 |
Liveness Probe
```

If the application crashes:

```
Liveness Probe Failed
        |
        |
Kubernetes Restarts Container
        |
        |
Application Works Again
```

---

# Conclusion

In this example:

✅ Created a Kubernetes Pod
✅ Configured an exec-based liveness probe
✅ Checked container health using a file
✅ Simulated failure by deleting the file
✅ Observed Kubernetes automatically restarting the container

Liveness probes are important for building reliable and self-healing Kubernetes applications.






=========================================================================
# welcome 


vi mylivenessprobe.yaml


apiVersion: v1
kind: Pod

metadata:
  name: mylivenessprobe
  labels:
    test: liveness

spec:
  containers:
    - name: liveness
      image: ubuntu

      args:
        - /bin/sh
        - -c
        - touch /tmp/healthy; sleep 1000

      livenessProbe:
        exec:
          command:
            - cat
            - /tmp/healthy

        initialDelaySeconds: 5
        periodSeconds: 5
        timeoutSeconds: 30


kubectl exec mylivenessprobe -it -- /bin/bash


cat /tmp/healthy


echo $?

output 0

check 

cat /tmp/bupinder

echo $?

output 0


I will delete this file 

kubectl exec mylivenessprobe -it -- /bin/bash

ls tmp/healthy

rm tmp/healthy

cat /tmp/bupinder

output: cat: /tmp/bupinder: No such file or directory


now cabernets will create the new pod automatically 




kubectl get pods


kubectl describe pod mylivenessprobe



Events:
  Type     Reason     Age                   From               Message
  ----     ------     ----                  ----               -------
  Normal   Scheduled  11m                   default-scheduler  Successfully assigned default/mylivenessprobe to minikube
  Normal   Pulled     11m                   kubelet            spec.containers{liveness}: Successfully pulled image "ubuntu" in 2.513s (2.513s including waiting). Image size: 120038753 bytes.
  Warning  Unhealthy  3m56s (x3 over 4m6s)  kubelet            spec.containers{liveness}: Liveness probe failed: cat: /tmp/healthy: No such file or directory
  Normal   Killing    3m56s                 kubelet            spec.containers{liveness}: Container liveness failed liveness probe, will be restarted
  Normal   Pulling    3m26s (x2 over 11m)   kubelet            spec.containers{liveness}: Pulling image "ubuntu"
  Normal   Created    3m23s (x2 over 11m)   kubelet            spec.containers{liveness}: Container created
  Normal   Started    3m23s (x2 over 11m)   kubelet            spec.containers{liveness}: Container started
  Normal   Pulled     3m23s                 kubelet            spec.containers{liveness}: Successfully pulled image "ubuntu" in 2.639s (2.639s including waiting). Image size: 120038753 bytes.
