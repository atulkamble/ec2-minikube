```

Prerequiste of minikube (t2.medium| = > 2 cpu cores)


0) Windows features ON/OFF

Hypervisors  >> install

restart 



1) powershell >> run as admin 

wsl --install

2) Docker Desktop Install 

3) Minikube install 

4) minikube start 

background - docker desktop runinng 

5) 

kubectl configured with k8s cluster



sudo yum update -y                    
sudo yum install docker -y                 
sudo systemctl enable docker
sudo systemctl start docker

# Install kubectl

curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"

chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Install Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

sudo usermod -aG docker $USER && newgrp docker

# Start Minikube with Docker driver
minikube start --driver=docker
OR
minikube start --driver=docker --cpus=2 --memory=4096

# Verify setup
minikube status
kubectl get nodes

minikube stop

minikube start --driver=docker --cpus=2 --memory=4096

kubectl get nodes
kubectl get pods -A

minikube addons enable default-storageclass
minikube addons enable storage-provisioner
minikube addons enable metrics-server
minikube addons enable dashboard
```
