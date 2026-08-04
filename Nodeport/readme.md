# Kubernetes NodePort Service Example

## Access Application Outside Kubernetes Cluster

### Architecture

```
User Browser
     |
     |
     v
NodePort Service
     |
     |
     v
Deployment
     |
     |
     v
httpd Pod
```

---

# Step 1: Create Deployment

Create file:

```bash
vi deployhttpd.yaml
```

Deployment configuration:

```yaml
kind: Deployment
apiVersion: apps/v1

metadata:
  name: mydeployments

spec:
  replicas: 1

  selector:
    matchLabels:
      name: deployment

  template:
    metadata:
      name: testpod1
      labels:
        name: deployment

    spec:
      containers:
      - name: c00
        image: httpd
        ports:
        - containerPort: 80
```

---

# Step 2: Create NodePort Service

Create file:

```bash
vi service.yaml
```

Service configuration:

```yaml
kind: Service
apiVersion: v1

metadata:
  name: demoservice

spec:
  ports:
  - port: 80
    targetPort: 80

  selector:
    name: deployment

  type: NodePort
```

---

# Step 3: Create Deployment

Run:

```bash
kubectl apply -f deployhttpd.yaml
```

---

# Step 4: Create Service

Run:

```bash
kubectl apply -f service.yaml
```

---

# Step 5: Check Service

```bash
kubectl get svc
```

---

# Step 6: Describe Service

```bash
kubectl describe svc demoservice
```

---

# Step 7: Access Application From Browser

Use:

```
http://192.168.21.2:32442
```

This one is important to see.

---

# Step 8: Access Using Minikube Service

Run:

```bash
minikube service demoservice
```

Output:

```
🎉  Opening service default/demoservice in default browser...
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.
```

---

# NodePort Flow

```
Browser
   |
   |
   v
Node IP : NodePort
(192.168.21.2:32442)
   |
   |
   v
demoservice
   |
   |
   v
Deployment
   |
   |
   v
httpd Pod
```

---

# Important Notes

- Deployment creates and manages Pods.
- Service provides stable access to Pods.
- NodePort exposes the Service outside the Kubernetes cluster.
- Browser can access the application using:

```
Node IP + NodePort
```

Example:

```
http://192.168.21.2:32442
```




# nodepote

create two files one deployhttpd.yaml


kind: Deployment
apiVersion: apps/v1

metadata:
  name: mydeployments

spec:
  replicas: 1

  selector:
    matchLabels:
      name: deployment

  template:
    metadata:
      name: testpod1
      labels:
        name: deployment

    spec:
      containers:
      - name: c00
        image: httpd
        ports:
        - containerPort: 80



-----------------------------------------------


another service 

vi service.yaml

kind: Service
apiVersion: v1

metadata:
  name: demoservice

spec:
  ports:
  - port: 80
    targetPort: 80

  selector:
    name: deployment

  type: NodePort




-----------------------------------------------


kubectl apply -f deployhttpd.yaml


kubectl apply -f service.yaml   

kubectl get svc

kubectl describe svc demoservice


http://192.168.21.2:32442

this one is important to see 

minikube service demoservice

🎉  Opening service default/demoservice in default browser...
❗  Because you are using a Docker driver on darwin, the terminal needs to be open to run it.


