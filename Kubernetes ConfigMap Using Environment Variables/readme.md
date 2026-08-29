# Kubernetes ConfigMap Using Environment Variables

## 📌 What are we doing?

In this example, we take a value from an existing **ConfigMap** and inject it into a Pod as an **environment variable**.

The flow is:

**ConfigMap → Environment Variable → Container**

---

## 1. Create the Pod YAML

Create the file:

```bash
vi deployenv.yaml
```

Add:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: myenvconfig

spec:
  containers:
  - name: c1
    image: centos:7

    command: ["/bin/bash", "-c", "while true; do echo Technical-Guftgu; sleep 5; done"]

    env:
    - name: MYENV
      valueFrom:
        configMapKeyRef:
          name: mymap
          key: sample.conf
```

### Important Parts

### `env`

```yaml
env:
- name: MYENV
```

This creates an environment variable called:

```text
MYENV
```

inside the container.

### `configMapKeyRef`

```yaml
valueFrom:
  configMapKeyRef:
    name: mymap
    key: sample.conf
```

This means:

> Get the value of `sample.conf` from the ConfigMap named `mymap` and store it in the environment variable `MYENV`.

**Important:** The ConfigMap `mymap` must already exist.

---

## 2. Create the Pod

```bash
kubectl apply -f deployenv.yaml
```

Check the Pod:

```bash
kubectl get pods
```

---

## 3. Enter the Container

```bash
kubectl exec myenvconfig -it -- /bin/bash
```

Now you are inside the container.

---

## 4. Check Environment Variables

Run:

```bash
env
```

You should find:

```text
MYENV=This is my configuration file
```

You can also directly check it:

```bash
echo $MYENV
```

Output:

```text
This is my configuration file
```

---

## 5. Exit the Container

```bash
exit
```

---

## 6. Delete the Pod

```bash
kubectl delete -f deployenv.yaml
```

---

# 🔑 Easy Concept to Remember

```text
ConfigMap
   ↓
sample.conf
   ↓
configMapKeyRef
   ↓
MYENV environment variable
   ↓
Container
```

### Simple Definition

**ConfigMap as Environment Variable:**
A ConfigMap value can be injected into a container as an **environment variable**.

### Volume vs Environment Variable

| Method                   | Where does the ConfigMap appear?       |
| ------------------------ | -------------------------------------- |
| **Volume**               | As a **file** inside the container     |
| **Environment Variable** | As a **variable** inside the container |

For example:

**Volume:**

```bash
cat /tmp/config/sample.conf
```

**Environment Variable:**

```bash
echo $MYENV
```

> **In short:** Instead of mounting the ConfigMap as a file, we use `configMapKeyRef` to put its value into an environment variable.
