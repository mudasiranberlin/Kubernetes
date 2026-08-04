# Kubernetes Pod-to-Pod Communication Using Service

## Two Day Task to Fix It

### Architecture

```
Frontend Pod  --->  Backend Service  --->  Backend Pod
```

---

# Step 1: Create Backend Pod

Create file:

```
backend-pod.yaml
```

Backend Pod configuration:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: backend-pod
  labels:
    app: backend

spec:
  containers:
  - name: backend-container
    image: nginx
    ports:
    - containerPort: 80
```

## Create Pod

```bash
kubectl apply -f backend-pod.yaml
```

## Check Pod

```bash
kubectl get pods
```

---

# Step 2: Create Service for Backend Pod

Create file:

```
backend-service.yaml
```

Backend Service configuration:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: backend-service

spec:

  selector:
    app: backend

  ports:
  - port: 80
    targetPort: 80

  type: ClusterIP
```

---

## Create Service

```bash
kubectl apply -f backend-service.yaml
```

## Check Service

```bash
kubectl get svc
```

---

Now Kubernetes gives a fixed name:

```
backend-service
```

The Service will forward traffic to the Backend Pod.

---

# Step 3: Create Frontend Pod

Create file:

```
frontend-pod.yaml
```

Frontend Pod configuration:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: frontend-pod

spec:

  containers:
  - name: frontend-container
    image: busybox

    command:
    - "/bin/sh"
    - "-c"

    - "while true; do sleep 3600; done"
```

---

## Create Frontend Pod

```bash
kubectl apply -f frontend-pod.yaml
```

## Check Pods

```bash
kubectl get pods
```

---

# Step 4: Test Pod → Service → Pod Communication

Remember:

```
frontend will replace with pod name for other
```

Enter frontend pod:

```bash
kubectl exec -it frontend-pod -- sh
```

---

Now call backend service:

```bash
wget -qO- backend-service
```

---

## Get Error

If curl is not available:

# Step 2: Install curl (if not available)

Inside testpod1:

```bash
apt update

apt install curl -y

curl 10.244.0.12:80
```

---

## Expected Output

```html
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>
```

---

# Communication Flow

```
Frontend Pod
     |
     |
     v
backend-service (ClusterIP)
     |
     |
     v
Backend Pod (nginx)
```

The Frontend Pod does not directly communicate with the Backend Pod IP.

It communicates through the Kubernetes Service:

```
backend-service
```

The Service provides a stable name and forwards requests to the Backend Pod.


Mudasir Ahmad



# Two day take to fix it

Frontend Pod  --->  Backend Service  --->  Backend Pod

Step 1: Create Backend Pod

Create file: backend-pod.yaml

apiVersion: v1
kind: Pod

metadata:
  name: backend-pod
  labels:
    app: backend

spec:
  containers:
  - name: backend-container
    image: nginx
    ports:
    - containerPort: 80
   


Create Pod:

kubectl apply -f backend-pod.yaml


check 

kubectl get pods


Step 2: Create Service for Backend Pod

Create file: backend-service.yaml


apiVersion: v1
kind: Service

metadata:
  name: backend-service

spec:

  selector:
    app: backend

  ports:
  - port: 80
    targetPort: 80

  type: ClusterIP


  
    --------------------------------------


Create Service:

kubectl apply -f backend-service.yaml


Check:

kubectl get svc



Now Kubernetes gives a fixed name:

backend-service


Step 3: Create Frontend Pod

Create file: frontend-pod.yaml


apiVersion: v1
kind: Pod

metadata:
  name: frontend-pod

spec:

  containers:
  - name: frontend-container
    image: busybox

    command:
    - "/bin/sh"
    - "-c"

    - "while true; do sleep 3600; done"
   
Create:

kubectl apply -f frontend-pod.yaml


kubectl get pods

Step 4: Test Pod → Service → Pod Communication

# remember this one frontend will replace with pod name for other 
Enter frontend pod:

kubectl exec -it frontend-pod -- sh


Now call backend service:

wget -qO- backend-service


get error 

Step 2: Install curl (if not available)

Inside testpod1:


apt update
apt install curl -y
curl 10.244.0.12:80 


Expected output:

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>

