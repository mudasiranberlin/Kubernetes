
# Kubernetes Pod-to-Pod Communication Example

## Create Two Pods and Communicate Between Them

### Architecture

```
Pod 1 (nginx)  ------------->  Pod 2 (httpd)

testpod1                      testpod4
```

---

# Step 1: Create First Pod (Nginx)

Create file:

```bash
vi pod1.yaml
```

Pod configuration:

```yaml
kind: Pod
apiVersion: v1

metadata:
  name: testpod1

spec:
  containers:
  - name: c01
    image: nginx
    ports:
    - containerPort: 80
```

---

# Step 2: Create Second Pod (Apache HTTP Server)

Create file:

```bash
vi pod2.yaml
```

Pod configuration:

```yaml
kind: Pod
apiVersion: v1

metadata:
  name: testpod4

spec:
  containers:
  - name: c03
    image: httpd
    ports:
    - containerPort: 80
```

---

# Step 3: Create Pods

Create first pod:

```bash
kubectl apply -f testpod1.yaml
```

Create second pod if you have deployment:

```bash
kubectl exec -it mydeployments -- sh 
```

---

# Step 4: Check Running Pods

```bash
kubectl get pods
```

Expected:

```
NAME        READY   STATUS
testpod1    1/1     Running
testpod4    1/1     Running
```

---

# Step 5: Enter Inside Pod

Enter testpod1:

```bash
kubectl exec -it testpod1 -- sh
```

or

```bash
kubectl exec -it testpod1 -- sh
```

---

# Step 6: Install Curl

Inside testpod1:

```bash
apt update

apt install curl -y
```

---

# Step 7: Access Another Pod Using Pod IP

Run:

```bash
curl 10.244.0.14:80
```

---


# Step 7: Now delete pods , services and deployments

Run:

```bash

kubectl delete pod backend-pod frontend-pod

kubectl delete svc backend-service

kubectl delete pod mydeployments-5b469d887b-fndc6
```

---


# Expected Output

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
testpod1 (nginx)
       |
       |
       v
Pod IP Address
       |
       |
       v
testpod4 (httpd)
```

---

## Important Notes

- Each Pod gets its own IP address.
- Pods can communicate directly using Pod IP.
- Pod IP can change when the Pod is recreated.
- For production communication, Kubernetes Service is recommended.



---------------------------------
vi pod1.yaml


kind: Pod
apiVersion: v1

metadata:
  name: testpod1

spec:
  containers:
  - name: c01
    image: nginx
    ports:
    - containerPort: 80
   
2nd pod

vi pod2.yaml

kind: Pod
apiVersion: v1

metadata:
  name: testpod4

spec:
  containers:
  - name: c03
    image: httpd
    ports:
    - containerPort: 80


kubectl apply -f testpod1.yaml

kubectl apply -f testpod4.yaml


kubectl get pods

kubectl exec -it testpod1 -- sh

or 

kubectl exec -it testpod1 -- sh


apt update
apt install curl -y


curl 10.244.0.14:80


Expected output:

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</html>
