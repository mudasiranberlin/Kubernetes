# Kubernetes


Install kubernets on mac so easy first install brew 


/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

then  brew install kubectl

kubectl version --client

install docker 

http://docs.docker.com/desktop/setup/install/mac-install/

minikube start

minikube dashboard 




now after that 

kubectl create deployment my-nginx --image=nginx:latest


kubectl get pods


kubectl expose deployment my-nginx --port=80 --type=LoadBalancer


kubectl get services

minikube service my-nginx



now 2nd Part 


minikube start/delete

minikube status

minikube dashboard

kubectl create deployment my-app --image=link
kubectl get deployments
kubectl get pods
kubectl delete deployment my-app

kubectl expose deployment my-app --
type=LoadBalancer --port=80

minikube service my-app

kubectl get services



