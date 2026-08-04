# Kubernetes EmptyDir Volume Sharing Between Containers

## Overview

This example demonstrates how two containers inside the same Kubernetes Pod can share data using an `emptyDir` volume.

### Architecture

```
              myvolemptydir Pod

        +-----------------------+
        |                       |
        |  Container c1         |
        |  /tmp/xchange         |
        |          |            |
        |          |            |
        |       emptyDir        |
        |          |            |
        |          |            |
        |  Container c2         |
        |  /tmp/data            |
        |                       |
        +-----------------------+
```

Both containers use the same `emptyDir` volume.

---

# Step 1: Create EmptyDir Pod

Create file:

```bash
vi emptydir.yaml
```

Pod configuration:

```yaml
apiVersion: v1
kind: Pod

metadata:
  name: myvolemptydir

spec:
  containers:

  - name: c1
    image: centos
    command: ["/bin/bash", "-c", "sleep 15000"]
    volumeMounts:
    - name: xchange
      mountPath: "/tmp/xchange"

  - name: c2
    image: centos
    command: ["/bin/bash", "-c", "sleep 10000"]
    volumeMounts:
    - name: xchange
      mountPath: "/tmp/data"

  volumes:
  - name: xchange
    emptyDir: {}
```

---

# Step 2: Create Pod

Run:

```bash
kubectl apply -f emptydir.yaml
```

---

# Step 3: Enter First Container (c1)

Access container `c1`:

```bash
kubectl exec myvolemptydir -c c1 -it -- /bin/bash
```

Go to `/tmp`:

```bash
cd /tmp
```

Check files:

```bash
ls
```

Enter shared volume:

```bash
cd xchange
```

---

# Step 4: Create File Inside Container c1

Create a file:

```bash
cat > mudasir
```

Check file:

```bash
ls
```

You will see:

```
mudasir
```

Exit from container:

```bash
exit
```

---

# Step 5: Enter Second Container (c2)

Access container `c2`:

```bash
kubectl exec myvolemptydir -c c2 -it -- /bin/bash
```

Go to `/tmp`:

```bash
cd /tmp
```

Check files:

```bash
ls
```

Enter shared volume:

```bash
cd data
```

Check files:

```bash
ls
```

---

# Expected Output

You will see the file created from another container:

```
mudasir
```

The file created inside container `c1` is available inside container `c2`.

---

# Step 6: Create File From Container c2

You can create a file here also:

```bash
cat > filename
```

Check:

```bash
ls
```

Exit:

```bash
exit
```

Now go back to container `c1` and check the file.

---

# EmptyDir Volume Flow

```
Container c1
     |
     |  /tmp/xchange
     |
     v
  emptyDir Volume
     |
     |  /tmp/data
     |
     v
Container c2
```

---

# Important Notes

- `emptyDir` creates temporary storage inside a Pod.
- All containers inside the same Pod can share this volume.
- Data remains available as long as the Pod exists.
- If the Pod is deleted, the data inside `emptyDir` is deleted.




-------------------------------------------------------------

vi emptydir.yaml


apiVersion: v1
kind: Pod

metadata:
  name: myvolemptydir

spec:
  containers:

  - name: c1
    image: centos
    command: ["/bin/bash", "-c", "sleep 15000"]
    volumeMounts:
    - name: xchange
      mountPath: "/tmp/xchange"

  - name: c2
    image: centos
    command: ["/bin/bash", "-c", "sleep 10000"]
    volumeMounts:
    - name: xchange
      mountPath: "/tmp/data"

  volumes:
  - name: xchange
    emptyDir: {}



kubectl apply -f emptydir.yaml

kubectl exec myvolemptydir -c c1 -it -- /bin/bash


cd /tmp

ls

cd xchange

create a file cat > mudasir

ls
then 

exit from container and go to second conatner

kubectl exec myvolemptydir -c c2 -it -- /bin/bash 


cd /tmp

ls

cd data

ls

you will seee the fits which u create in another container

you can create file here also then exit then go to container 1
