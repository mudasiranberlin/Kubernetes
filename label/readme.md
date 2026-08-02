Now we will be starting about the labels in kubernets 


vi pod.yaml 

kind: Pod
apiVersion: v1
metadata:
  name: testpod
  labels:
   env: development
   class: pods

spec:
  containers:
  - name: c00
    image: ubuntu
    command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]

kubectl apply -f pod.yaml


here only you have to add :

metadata:
  name: testpod
  labels:    //this one have to add
   env: development
   class: pods

kubectl apply -f pod.yaml
kubectl get pods -o wide  # here you will get pod name as test pod so use in describe

kubectl describe pod testpod


kubectl logs -f testpod  // what run in container


kubectl delete pod testpod


now check labels 

kubectl get pods --show-labels


now I will be create another pod without label 

vi addlabel.yaml

kind: Pod
apiVersion: v1
metadata:
  name: addlabel

spec:
  containers:
  - name: c00
    image: ubuntu
    command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]


so I can add label using the command 

kubectl label pods addlabel myname=google

now the label name is google
now select using selector using label 

kubectl get pods -l env=development

when env =development  

now when we do not have means not equal to development we want to find other than development 



kubectl get pods -l env!=development


you can delete the pod using the sanme command 

kubectl delete pods -l env=development



now I will check with in 

kubectl get pods -l 'env in (frontend)'

kubectl get pods -l 'env in (frontend,development)'

kubectl get pods -l 'env notin (frontend,development)'

kubectl get pods -l class=pods

 kubectl get pods -l class=pods,env=frontend



 and now we have to choose hardware where we can install the container 

 kind: Pod
apiVersion: v1

metadata:
  name: mudo
  labels:
    env: frontend
    class: pods

spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]

  nodeSelector:
    hardware: t2-medium


kubectl apply -f pod4.yaml

kubectl describe pod mudo

now delete 

kubectl delete pods mudi 

two container delete

kubectl delete pods mudi mudo




Now we will be talking about scaling nd replications


vi pod5.yaml

kind: ReplicationController
apiVersion: v1

metadata:
  name: mudi

spec:
  replicas: 5

  selector:
    env: frontend

  template:
    metadata:
      labels:
        env: frontend

    spec:
      containers:
        - name: c00
          image: ubuntu
          command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]

kubectl apply -f pod5.yaml

kubectl get rc

get all 5 replicas 


kubectl describe rc 

kubectl describe rc mudi


delete one pod and then you will see it will create again 


if you want to run now only 2 pods then u can go to vi pod5 and then relica 2
save and then apply 
kubectl apply -f pod5.yaml

or you can do it using the command 
I will show you that tooo 


kubectl scale --replicas=8 rc -l env=frontend 


kubectl delete -f pod5.yaml

now all pods are deleted

now start with the replica set


kind: ReplicaSet
apiVersion: apps/v1

metadata:
  name: mudi-rs

spec:
  replicas: 5

  selector:
    matchLabels:
      env: frontend

  template:
    metadata:
      labels:
        env: frontend

    spec:
      containers:
        - name: c00
          image: ubuntu
          command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]


  kubectl apply -f pod6.yaml

  kubectl get pods

  kubectl get rs


when to increase or decrease replica

  metadata:
  name: mudi-rs

  kubectl scale --replicas=1 rs/mudi-rs

delete replica set 


  kubectl delete rs/mudi-rs








