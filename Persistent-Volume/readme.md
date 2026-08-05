vi mypres.yaml



apiVersion: v1
kind: PersistentVolume
metadata:
  name: myebsvol

spec:
  capacity:
    storage: 1Gi

  accessModes:
    - ReadWriteOnce

  persistentVolumeReclaimPolicy: Recycle

  awsElasticBlockStore:
    volumeID: vol-0a3264e6f82b5cd34   # Replace with your EBS Volume ID
    fsType: ext4


  =========================

  kubectl apply -f mypress.yaml

  kubectl get pv

create another file 

vi myopic.yaml

===========================

  apiVersion: v1
kind: PersistentVolumeClaim

metadata:
  name: myebsvolclaim

spec:
  accessModes:
    - ReadWriteOnce

  resources:
    requests:
      storage: 1Gi


====================================
kubectl apply -f mypvc.yaml


now create 3rd file 




apiVersion: apps/v1
kind: Deployment

metadata:
  name: pvdeploy

spec:
  replicas: 1

  selector:
    matchLabels:
      app: mypv

  template:
    metadata:
      labels:
        app: mypv

    spec:
      containers:
      - name: shell
        image: centos
        command: ["/bin/bash", "-c", "sleep 10000"]

        volumeMounts:
        - name: mypd
          mountPath: "/tmp/persistent"

      volumes:
      - name: mypd
        persistentVolumeClaim:
          claimName: myebsvolclaim
