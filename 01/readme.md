# 
kubectl get namespace

kubectl get pods -n kube-system

kubectl create ns demo

create new namespace  demo 

vi pod.yaml

kubectl apply -f pod.yaml

kubectl get pods

 kubectl get pods -n demo

 
kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml




kubectl top nodes


In Kubernetes, requests and limits are used to control how much CPU and memory a container gets.

apiVersion: apps/v1



resources:
  requests:
    cpu: "100m"
    memory: "128Mi"

  limits:
    cpu: "500m"
    memory: "512Mi"


   Requests = Minimum needed

A request tells Kubernetes:

“My container needs at least this much resource.”

requests:
  cpu: "100m"
  memory: "128Mi"


  

  This means Kubernetes will try to find a Node with at least:

100m CPU = 0.1 CPU core
128Mi memory

Kubernetes uses requests when scheduling Pods.



Limits = Maximum allowed

A limit tells Kubernetes:

“My container cannot use more than this amount.”

limits:
  cpu: "500m"
  memory: "512Mi"


  


The container can use up to:

500m CPU = 0.5 CPU core
512Mi memory
