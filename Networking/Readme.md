# hello 


talk with each other when we create 2 container 


Can create new file 
vi podd9.yaml


apiVersion: v1
kind: Pod

metadata:
  name: testpod

spec:
  containers:

  - name: c00
    image: ubuntu
    command:
      - "/bin/bash"
      - "-c"
      - "while true; do echo Hello-Bhupinder; sleep 5; done"

  - name: c01
    image: httpd
    ports:
      - containerPort: 80










kubectl exec testpod -it -c c00 -- /bin/bash

apt update && apt install curl


curl localhost:80


pod to pod communication 


vi pod11.yaml

apiVersion: v1
kind: Pod

metadata:
  name: testpod1

spec:
  containers:
    - name: c02
      image: httpd
      command:
        - "/bin/bash"
        - "-c"
        - "while true; do echo Hello-Bhupinder; sleep 5; done"
      ports:
        - containerPort: 80
~                             


vi pod12.yaml



apiVersion: v1
kind: Pod

metadata:
  name: testpod2

spec:
  containers:
    - name: c02
      image: httpd
      command:
        - "/bin/bash"
        - "-c"
        - "while true; do echo Hello-Bhupinder; sleep 5; done"
      ports:
        - containerPort: 80
~                             




apply and save 


curl 10.244.0.70:80


you will see the apache page 





