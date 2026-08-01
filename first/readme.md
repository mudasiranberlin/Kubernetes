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

  restartPolicy: Never

