1. Install Docker
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

# Allow current user to use Docker without sudo
sudo usermod -aG docker $USER
newgrp docker



Verify:

docker --version



2. Install kubectl

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl

sudo mv kubectl /usr/local/bin/


Verify:

kubectl version --client


3. Install Minikube

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube


minikube version


4. Install conntrack

sudo apt install -y conntrack



5. Start Minikube
If using Docker Driver (Recommended)


minikube start --driver=docker


If using EC2 without virtualization (Older method)

sudo minikube start --driver=none

6. Check Status

minikube status

Expected output:

host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured


7. Test Kubernetes
kubectl get nodes




# Complete Installation Commands


sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker $USER
newgrp docker

curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

sudo apt install -y conntrack

minikube start --driver=docker


#


ANberlin
