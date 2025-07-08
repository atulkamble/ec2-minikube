# 🐳 Kubernetes Minikube Setup on Windows/Linux (Docker Driver)

This project documents how to install and configure **Minikube** — a lightweight Kubernetes implementation — using **Docker** as a driver, on both **Windows (WSL2)** and **Linux** environments.

---

## 📋 Prerequisites

* **Minimum instance type:** `t2.medium` or higher (≥ **2 CPU cores**)
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
