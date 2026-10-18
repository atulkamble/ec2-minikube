# Minikube Kubernetes Practice Lab

Objective: Install Minikube, create a local Kubernetes cluster, and practice Pods, Deployments, Services, scaling, networking, and troubleshooting.

Prerequisites: Docker, Minikube, and kubectl installed.

## 1. Install Minikube

macOS

```
brew install minikube
brew install kubectl
brew install --cask docker
```

Windows (PowerShell)

```
winget install Kubernetes.minikube
winget install Kubernetes.kubectl
winget install Docker.DockerDesktop
```

Amazon Linux 2023 (x86_64)

```
sudo dnf update -y

curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64

sudo install minikube-linux-amd64 /usr/local/bin/minikube

minikube version
```

On Amazon Linux, also install and configure a supported container or VM driver, such as Docker. For Docker, use a non-root user with Docker access. An EC2 instance must have sufficient CPU, RAM, and disk space.

## 2. Start Minikube Cluster

Start Docker Desktop first on macOS or Windows.

```
minikube start --driver=docker

minikube status
kubectl cluster-info
kubectl get nodes
kubectl get nodes -o wide
```

## 3. Practice Kubernetes Pods

```
# Create Pod
kubectl run mypod --image=nginx

# List Pods
kubectl get pods
kubectl get pods -o wide

# Pod details
kubectl describe pod mypod

# View logs
kubectl logs mypod

# Access container
kubectl exec -it mypod -- /bin/sh

# Delete Pod
kubectl delete pod mypod
```

## 4. Practice Deployments

```
# Create Deployment
kubectl create deployment myapp --image=nginx

# View Deployment
kubectl get deployments

# View Pods
kubectl get pods

# Scale Deployment
kubectl scale deployment myapp --replicas=3

# Verify
kubectl get pods -o wide

# Deployment details
kubectl describe deployment myapp
```

## 5. Practice Kubernetes Services

```
# Expose Deployment
kubectl expose deployment myapp \
  --type=NodePort \
  --port=80 \
  --target-port=80

# List Services
kubectl get svc

# Get application URL
minikube service myapp --url
```

On Docker Desktop, keep the terminal running if Minikube creates a temporary service tunnel.

## 6. Practice YAML Deployment

Create `deployment.yaml`:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:stable
          ports:
            - containerPort: 80
```

Apply and verify:

```
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods
```

## 7. Practice ClusterIP, NodePort and LoadBalancer

```
# ClusterIP
kubectl expose deployment nginx-deployment \
  --name=nginx-clusterip \
  --type=ClusterIP \
  --port=80

# NodePort
kubectl expose deployment nginx-deployment \
  --name=nginx-nodeport \
  --type=NodePort \
  --port=80

# LoadBalancer
kubectl expose deployment nginx-deployment \
  --name=nginx-loadbalancer \
  --type=LoadBalancer \
  --port=80

# Check Services
kubectl get svc
```

For LoadBalancer testing, open a separate terminal:

```
minikube tunnel
```

Then check:

```
kubectl get svc nginx-loadbalancer
```

## 8. Practice Rolling Updates and Rollback

```
# Update image
kubectl set image deployment/myapp nginx=nginx:1.27

# Check rollout
kubectl rollout status deployment/myapp

# View history
kubectl rollout history deployment/myapp

# Rollback
kubectl rollout undo deployment/myapp
```

The container name in the `myapp` Deployment created by `kubectl create deployment` is `nginx`.

## 9. Practice Namespace

```
kubectl create namespace dev

kubectl create deployment webapp \
  --image=nginx \
  -n dev

kubectl get pods -n dev

kubectl get all -n dev

kubectl delete namespace dev
```

## 10. Practice Minikube Addons and Dashboard

```
# List addons
minikube addons list

# Enable metrics server
minikube addons enable metrics-server

# View metrics
kubectl top nodes
kubectl top pods

# Enable ingress
minikube addons enable ingress

# Open dashboard
minikube dashboard
```

Metrics may take a short time to become available.

## 11. Practice Troubleshooting

```
kubectl get events --sort-by=.metadata.creationTimestamp

kubectl describe pod mypod

kubectl logs mypod

kubectl get pods -A

kubectl get endpointslices

kubectl get nodes

kubectl cluster-info

minikube logs
```

## 12. Practice Minikube Cluster Management

```
# Cluster status
minikube status

# SSH into Minikube node
minikube ssh

# Show node IP
minikube ip

# List profiles
minikube profile list

# Stop cluster
minikube stop

# Start cluster again
minikube start

# Delete cluster
minikube delete
```

# 🐳 Kubernetes Minikube Setup on Windows/Linux (Docker Driver)

This project documents how to install and configure **Minikube** — a lightweight Kubernetes implementation — using **Docker** as a driver, on both **Windows (WSL2)** and **Linux** environments.

---
## 📸 Screenshot

![minikube](https://github.com/atulkamble/ec2-minikube/blob/main/minikube.png)

---

## 📋 Prerequisites

* **Minimum instance type:** `t3.large` or higher (≥ **2 CPU cores**)
* **Docker Desktop** installed and running in the background

---

## 🖥️ Windows Setup

### 0️⃣ Enable Windows Features

* Go to **Windows Features** → Turn on:

  * **Hypervisor Platform**
  * **Virtual Machine Platform**
  * **Windows Subsystem for Linux**

📌 Restart your system.

---

### 1️⃣ Install WSL2

Open **PowerShell** as Administrator and run:

```bash
wsl --install
```

---

### 2️⃣ Install Docker Desktop

* Download and install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
* Enable WSL2 integration during setup.

---

### 3️⃣ Install Minikube

Follow [Minikube official install guide](https://minikube.sigs.k8s.io/docs/start/) for Windows or see the Linux section below for CLI instructions.

---

### 4️⃣ Start Minikube

Ensure Docker Desktop is running, then start Minikube:

```bash
minikube start --driver=docker --cpus=2 --memory=4096
```

---

### 5️⃣ Verify kubectl & Minikube

```bash
minikube status
kubectl get nodes
kubectl get pods -A
```

---

## 🐧 Linux Setup

### Update & Install Docker

```bash
sudo yum update -y
sudo yum install docker -y
sudo systemctl enable docker
sudo systemctl start docker
```

---

### Install kubectl

```bash
curl -LO "https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
```

---

### Install Minikube

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Add your user to the Docker group:

```bash
sudo usermod -aG docker $USER && newgrp docker
```

---

### Start Minikube

```bash
minikube start --driver=docker --cpus=2 --memory=4096
```

---

### Verify Setup

```bash
minikube status
kubectl get nodes
kubectl get pods -A
```

---

## 🔧 Optional Addons

Enable useful Minikube addons:

```bash
minikube addons enable default-storageclass
minikube addons enable storage-provisioner
minikube addons enable metrics-server
minikube addons enable dashboard
```

---

## 📊 Access Kubernetes Dashboard

```bash
minikube dashboard
```

---

## 🛑 Stop Minikube

```bash
minikube stop
```

---

## 📌 Notes

* Ensure **Docker Desktop** is running before starting Minikube.
* Recommended specs: 2+ vCPUs, 4GB+ RAM
* Supports **Docker driver** for isolated, fast local Kubernetes clusters.

---

## 📜 License

This guide is released under the [MIT License](LICENSE)

---

## 👨‍💻 Author

**Atul Kamble**

- 💼 [LinkedIn](https://www.linkedin.com/in/atuljkamble)
- 🐙 [GitHub](https://github.com/atulkamble)
- 🐦 [X](https://x.com/Atul_Kamble)
- 📷 [Instagram](https://www.instagram.com/atuljkamble)
- 🌐 [Website](https://www.atulkamble.in)

---


## 🙌 Contributing

Contributions are welcome! Feel free to open issues or submit pull requests to improve this project.
