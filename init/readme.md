# Kubernetes Init Container

## 📌 What is an Init Container?

An **Init Container** runs **before the main container**.

It is used to prepare something before the application starts.

```text
Init Container
      ↓
   Complete
      ↓
Main Container
      ↓
   Running
```

---

## 1. Create YAML

```bash
vi init.yaml
```

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: initcontainer

spec:
  initContainers:
    - name: c1
      image: centos:7
      command:
        - /bin/sh
        - -c
        - "echo LIKE AND SUBSCRIBE TECHNICAL GUFTGU > /tmp/xchange/testfile; sleep 30"

      volumeMounts:
        - name: xchange
          mountPath: /tmp/xchange

  containers:
    - name: c2
      image: centos:7
      command:
        - /bin/bash
        - -c
        - "while true; do cat /tmp/data/testfile; sleep 5; done"

      volumeMounts:
        - name: xchange
          mountPath: /tmp/data

  volumes:
    - name: xchange
      emptyDir: {}
```

---

# 2. What Happens?

### Init Container `c1`

It creates:

```text
/tmp/xchange/testfile
```

with:

```text
LIKE AND SUBSCRIBE TECHNICAL GUFTGU
```

Then it waits for 30 seconds.

```text
c1
 ↓
Create file
 ↓
Sleep 30 seconds
 ↓
Complete
```

### Main Container `c2`

Only after `c1` completes, `c2` starts.

It continuously reads:

```text
/tmp/data/testfile
```

and prints the content every 5 seconds.

---

# 3. `emptyDir`

Both containers use the same volume:

```yaml
volumes:
  - name: xchange
    emptyDir: {}
```

`c1` uses:

```text
/tmp/xchange
```

`c2` uses:

```text
/tmp/data
```

But both point to the same `xchange` volume.

```text
c1
 |
 | /tmp/xchange/testfile
 |
 ↓
emptyDir
 |
 ↓
c2
 |
 | /tmp/data/testfile
```

---

# 4. Create Pod

```bash
kubectl apply -f init.yaml
```

---

# 5. Watch Pod

```bash
watch kubectl get pods
```

Initially:

```text
initcontainer   0/1   Init:0/1
```

After 30 seconds:

```text
initcontainer   1/1   Running
```

---

# 6. Check Logs

Main container:

```bash
kubectl logs -f pod/initcontainer -c c2
```

Output:

```text
LIKE AND SUBSCRIBE TECHNICAL GUFTGU
LIKE AND SUBSCRIBE TECHNICAL GUFTGU
LIKE AND SUBSCRIBE TECHNICAL GUFTGU
```

---

# 7. Enter Container

```bash
kubectl exec -it initcontainer -c c2 -- /bin/bash
```

Check the file:

```bash
cat /tmp/data/testfile
```

Output:

```text
LIKE AND SUBSCRIBE TECHNICAL GUFTGU
```

---

# 8. Useful Commands

```bash
kubectl get pods
```

```bash
kubectl describe pod initcontainer
```

```bash
kubectl logs initcontainer -c c1
```

```bash
kubectl logs -f initcontainer -c c2
```

```bash
kubectl delete pod initcontainer
```

---

# 🧠 Remember

### Init Container

**Runs first and prepares something.**

### Main Container

**Runs after Init Container completes.**

### `emptyDir`

**Allows containers in the same Pod to share files.**

```text
Init Container
      ↓
  Create File
      ↓
   Complete
      ↓
Main Container
      ↓
  Read File
      ↓
   Running
```










# init 

vi init.yaml

apiVersion: v1
kind: Pod

metadata:
  name: initcontainer

spec:
  initContainers:
    - name: c1
      image: centos:7
      command:
        - /bin/sh
        - -c
        - "echo LIKE AND SUBSCRIBE TECHNICAL GUFTGU > /tmp/xchange/testfile; sleep 30"
      volumeMounts:
        - name: xchange
          mountPath: /tmp/xchange

  containers:
    - name: c2
      image: centos:7
      command:
        - /bin/bash
        - -c
        - "while true; do cat /tmp/data/testfile; sleep 5; done"
      volumeMounts:
        - name: xchange
          mountPath: /tmp/data

  volumes:
    - name: xchange
      emptyDir: {}


kubectl apply -f init.yaml 

watch kubectl get pod

kubectl logs -f pod/initcontainer
