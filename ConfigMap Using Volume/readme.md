ConfigMap Using Volume# Kubernetes ConfigMap Using Volume

## 📌 What are we doing?

In this example, we create a **ConfigMap from a file** and then mount that ConfigMap inside a Pod as a **volume**.

The flow is:

**sample.conf → ConfigMap → Pod Volume → `/tmp/config/sample.conf`**

---

## 1. Create the Configuration File

Create a file:

```bash
vi sample.conf
```

Write:

```text
This is my configuration file
```

Save and exit.

---

## 2. Create the ConfigMap

Create a ConfigMap from the file:

```bash
kubectl create configmap mymap --from-file=sample.conf
```

Here:

* `mymap` = ConfigMap name
* `--from-file=sample.conf` = takes the contents of `sample.conf` and stores it in the ConfigMap

Check it:

```bash
kubectl get configmap
```

Get more details:

```bash
kubectl describe configmap mymap
```

You should see `sample.conf` and its contents.

---

## 3. Create the Pod YAML

Create the file:

```bash
vi deployconfigmap.yaml
```

Add:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: myvolconfig

spec:
  containers:
  - name: c1
    image: centos:7

    command: ["/bin/bash", "-c", "while true; do echo Technical-Guftgu; sleep 5; done"]

    volumeMounts:
    - name: testconfigmap
      mountPath: "/tmp/config"

  volumes:
  - name: testconfigmap
    configMap:
      name: mymap

      items:
      - key: sample.conf
        path: sample.conf
```

### Important Parts

**`volumes`**

```yaml
volumes:
- name: testconfigmap
  configMap:
    name: mymap
```

This tells Kubernetes:

> "Create a volume using the ConfigMap called `mymap`."

**`volumeMounts`**

```yaml
volumeMounts:
- name: testconfigmap
  mountPath: "/tmp/config"
```

This tells Kubernetes:

> "Mount that ConfigMap volume inside the container at `/tmp/config`."

**`items`**

```yaml
items:
- key: sample.conf
  path: sample.conf
```

This tells Kubernetes which ConfigMap key should become a file and what the file should be called.

---

## 4. Create the Pod

```bash
kubectl apply -f deployconfigmap.yaml
```

Check the Pod:

```bash
kubectl get pods
```

---

## 5. Enter the Pod

```bash
kubectl exec myvolconfig -it -- /bin/bash
```

Now you are inside the container.

---

## 6. Check the Mounted File

Go to the mounted directory:

```bash
cd /tmp/config
```

Check the file:

```bash
ls
```

Output:

```text
sample.conf
```

Read the file:

```bash
cat sample.conf
```

Output:

```text
This is my configuration file
```

### What happened?

The original file:

```text
sample.conf
```

was used to create:

```text
ConfigMap: mymap
```

Then Kubernetes mounted the ConfigMap into the Pod:

```text
/tmp/config/sample.conf
```

So the container can read the configuration file.

---

## 7. Delete the Pod

After testing:

```bash
kubectl delete -f deployconfigmap.yaml
```

---

# 🔑 Easy Concept to Remember

```text
sample.conf
     ↓
ConfigMap (mymap)
     ↓
Volume
     ↓
Pod
     ↓
/tmp/config/sample.conf
```

### Simple Definition

**ConfigMap:** Stores configuration data such as files, settings, and environment values.

**ConfigMap as Volume:** Allows Kubernetes to make ConfigMap data available as **files inside a container**.

> **In short:** We created a configuration file → stored it in a ConfigMap → mounted the ConfigMap as a volume → accessed the file inside the Pod.
