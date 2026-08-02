vi pod.yaml
kubectl apply -f pod.yaml
kubectl get pods -o wide  # here you will get pod name as test pod so use in describe

kubectl describe pod testpod


kubectl logs -f testpod  // what run in container


kubectl logs -f testpod -c c00         when u have multiple container then use -c and c00 is container name 


kubectl delete pod testpod

add annotation 

vi pod.yaml 

kind: Pod
apiVersion: v1

metadata:
  name: testpod
  annotations:      # here annotations and description any thing u can write 
   description: hamara phela test pod

spec:
  containers:
  - name: c00
    image: ubuntu
    command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]

  restartPolicy: Never



  kubectl describe pod testpod  # check annotation here 


  # and now we will create the 2 pods 

  vi pod.yaml


  kind: Pod
apiVersion: v1

metadata:
  name: testpod
  annotations: 
   description: hamara phela test pod

spec:
  containers:
  - name: c00
    image: ubuntu
    command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]
  - name: c01.     # this one is 2nd container 
    image: ubuntu
    command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5; done"]


  now after that  run command 

  kubectl apply -f pod.yaml

  kubectl get pods   # now you will see 2 container

  kubectl describe pod testpod


  kubectl logs -f testpod  # testpod is the name given to my pod u can check the name in describe and you will see details of 2 containers


 kubectl logs -f testpod  -c c00 # u can change the container name 

 kubectl exec testpod  -c c01 -- hostname -i
 # this command give you I-address 


 kubectl exec testpod -it  -c c01 -- bin/bash   # Go inside the container

  kubectl delete pod testpod 


  Now we are using the environment variable :

  
kind: Pod
apiVersion: v1
metadata:
  name: environments
spec:
  containers:
    - name: c00
      image: ubuntu
      command: ["/bin/bash", "-c", "while true; do echo Hello-Bhupinder; sleep 5 ; done"]
      env:                        # List of environment variables to be used inside the pod
      - name: MYNAME
        value: BHUPINDER



save and 

kubectl apply -f pod.yaml

  kubectl get pods   # now you will see 2 container

  kubectl describe pod testpod

kubectl exec environments -it -- bin/bash

inside the container 

env

echo $MYNAME
output your name 


install apache 

kind: Pod
apiVersion: v1

metadata:
  name: testpod4

spec:
  containers:
    - name: c00
      image: httpd
      ports:
        - containerPort: 80




        
