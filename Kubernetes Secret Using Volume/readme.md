# Kubernetes Secret Using Volume

## 📌 What are we doing?

In this example, we create a **Kubernetes Secret** from username and password files and then mount the Secret inside a Pod as a **volume**.

### Flow

```text
username.txt + password.txt
          ↓
      Kubernetes Secret
          ↓
       Secret Volume
          ↓
          Pod
          ↓
   /tmp/mysecrets/
```

---

## 1. Create Username and Password Files

Create a username file:

```bash
echo "root" > username.txt
```

Create a password file:

```bash
echo "mypassword123" > password.txt
```

Now we have:

```text
username.txt
password.txt
```

---

## 2. Create the Secret

Create a Secret using both files:

```bash
kubectl create secret generic mysecret \
  --from-file=username.txt \
  --from-file=password.txt
```

### What does this mean?

* `secret` → We are creating a Kubernetes Secret.
* `generic` → Creates a generic Secret.
* `mysecret` → Name of the Secret.
* `--from-file=username.txt` → Stores the username file.
* `--from-file=password.txt` → Stores the password file.

---

## 3. Check the Secret

List Secrets:

```bash
kubectl get secret
```

You should see:

```text
mysecret
```

Get more information:

```bash
kubectl describe secret mysecret
```

> `describe` shows the Secret information, but it does **not show the actual secret values**.

---

## 4. Create the Pod YAML

Create the file:

```bash
vi deploysecret.yaml
```

Add:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: myvolsecret

spec:
  containers:
  - name: c1
    image: centos:7

    command: ["/bin/bash", "-c", "while true; do echo Technical-guftgu; sleep 5; done"]

    volumeMounts:
    - name: testsecret
      mountPath: "/tmp/mysecrets"

  volumes:
  - name: testsecret
    secret:
      secretName: mysecret
```

---

## 5. Important YAML Parts

### `volumes`

```yaml
volumes:
- name: testsecret
  secret:
    secretName: mysecret
```

This tells Kubernetes:

> Create a volume using the Secret called `mysecret`.

### `volumeMounts`

```yaml
volumeMounts:
- name: testsecret
  mountPath: "/tmp/mysecrets"
```

This tells Kubernetes:

> Mount the Secret volume inside the container at `/tmp/mysecrets`.

The Secret files will appear inside this directory.

---

## 6. Create the Pod

Run:

```bash
kubectl apply -f deploysecret.yaml
```

Check the Pod:

```bash
kubectl get pods
```

---

## 7. Enter the Pod

```bash
kubectl exec myvolsecret -it -- /bin/bash
```

Now you are inside the container.

---

## 8. Check the Secret Files

Go to the mounted directory:

```bash
cd /tmp/mysecrets
```

Check the files:

```bash
ls
```

You should see:

```text
password.txt
username.txt
```

Read the password:

```bash
cat password.txt
```

Output:

```text
mypassword123
```

You can also check the username:

```bash
cat username.txt
```

Output:

```text
root
```

---

## 🔑 Easy Concept to Remember

```text
username.txt
password.txt
      ↓
  Secret: mysecret
      ↓
 Secret Volume
      ↓
     Pod
      ↓
 /tmp/mysecrets/
      ↓
username.txt
password.txt
```

### Simple Definition

**Kubernetes Secret:**
A Kubernetes Secret is used to store sensitive information such as **passwords, usernames, tokens, and keys**.

**Secret as Volume:**
A Secret can be mounted into a Pod as **files**.

### ConfigMap vs Secret

| ConfigMap                 | Secret                  |
| ------------------------- | ----------------------- |
| Normal configuration data | Sensitive data          |
| Application settings      | Passwords               |
| Configuration files       | Tokens / keys           |
| Can be mounted as files   | Can be mounted as files |

---

## 🎯 Interview Point

If asked:

**"How can you use a Secret inside a Pod?"**

You can answer:

> A Kubernetes Secret can be consumed by a Pod as **environment variables** or mounted as **files through a volume**.

---

## 🧹 Cleanup

Exit the container:

```bash
exit
```

Delete the Pod:

```bash
kubectl delete -f deploysecret.yaml
```

You can also delete the Secret:

```bash
kubectl delete secret mysecret
```

---

## 👨‍💻 Author

**Name:** Mudasir Ahmad
**Location:** Phnom Penh, Cambodia
**Topic:** Kubernetes / Secrets
**Purpose:** Learning & Interview Preparation
