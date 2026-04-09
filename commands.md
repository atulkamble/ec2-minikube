```
// setup minikube on EC2 | c7.iflex.large | t3.medium 

References: 
https://minikube.sigs.k8s.io/docs/start/?arch=%2Flinux%2Fx86-64%2Fstable%2Frpm+package
https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

sudo yum update -y 
sudo yum upgrade -y 
sudo yum install docker -y 
sudo systemctl start docker 
sudo systemctl enable docker 
sudo usermod -aG docker $USER 
newgrp docker
docker images
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-latest.x86_64.rpm
sudo rpm -Uvh minikube-latest.x86_64.rpm
minikube start 
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
kubectl version --client
kubectl get nodes 
   
```
