# deployment

create new file vi pod7.yaml





kind: Deployment
apiVersion: apps/v1

metadata:
  name: mudi-deployment

spec:
  replicas: 3

  selector:
    matchLabels:
      app: frontend

  template:
    metadata:
      labels:
        app: frontend

    spec:
      containers:
        - name: c00
          image: ubuntu
          command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]

2nd file you can also use :


kind: Deployment
apiVersion: apps/v1

metadata:
  name: mydeployments

spec:
  replicas: 2

  selector:
    matchLabels:
      name: deployment

  template:
    metadata:
      name: testpod
      labels:
        name: deployment

    spec:
      containers:
        - name: c00
          image: ubuntu
          command: ["/bin/bash", "-c", "while true; do echo Technical-Guftgu; sleep 5; done"]

kubectl apply -f pod8.yaml
kubectl get pods
kubectl get deployment 

check 
kubectl describe deployment mydeployments


kubectl delete pods  (name deployment)

you will see after delete it will create again to maintain its state 

now to scale  mydeployemet which I have give the name 

kubectl scale --replicas=1 deployment mydeployments


kubectl logs -f mydeployments-bf685769-9t52z 


now go to vi pod8.yaml

and make changes there and then 

apply again and see the changes 

kubectl get deploy

you willl seee the 2 deployments and 1 will have 0 desired state and another one have the 2 state and desired 


kubectl get rs


kubectl get deploy


if u run the one pod and then like this 

I make changes in the pod text and you will se the midair is good there 

kubectl logs -f mydeployments-c7897f97f-4nv9z


check os 

replace with your pod name (mydeployments-c7897f97f-4nv9z)

kubectl exec mydeployments-c7897f97f-4nv9z -- cat /etc/os-release


most important command to roll back to previous versions 

kubectl rollout status deployment mydeployments



kubectl rollout history deployment mydeployments

kubectl rollout undo deploy/mydeployments
