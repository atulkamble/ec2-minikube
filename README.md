# EC2 and Minikube Kubernetes Practice Lab

A beginner-friendly lab for setting up a local Kubernetes cluster with Minikube, deploying NGINX, and practicing core Kubernetes operations.

> Minikube is intended for local learning, development, and testing. This lab is not a production Kubernetes setup.

## Learning objectives

- Install Minikube and kubectl
- Start and inspect a local Kubernetes cluster
- Create Pods and Deployments
- Expose applications with Kubernetes Services
- Practice scaling, rolling updates, namespaces, and troubleshooting
- Deploy NGINX using YAML manifests

## Architecture

```text
User → NodePort Service → Deployment → NGINX Pods
```

## Prerequisites

Install and start Docker before starting Minikube.

Recommended resources for the host:

- 2 or more CPU cores
- 4 GB or more available memory
- Internet access

Check your tools:

```bash
docker --version
minikube version
kubectl version --client
```

## 1. Install the tools

### macOS

```bash
brew install minikube kubectl
brew install --cask docker
```

Start Docker Desktop after installation.

### Windows with WSL2

1. Install WSL2 from an Administrator PowerShell window:

   ```powershell
   wsl --install
   ```

2. Restart Windows if prompted.
3. Install Docker Desktop and enable WSL2 integration.
4. Install Minikube and kubectl using the official guides:

   - [Install Minikube](https://minikube.sigs.k8s.io/docs/start/)
   - [Install kubectl](https://kubernetes.io/docs/tasks/tools/)

Start Docker Desktop before continuing.

### Amazon Linux 2023 (x86_64)

Install Docker:

```bash
sudo dnf update -y
sudo dnf install -y docker
sudo systemctl enable --now docker
sudo usermod -aG docker "$USER"
```

Log out and back in so the Docker group membership takes effect. Confirm Docker works:

```bash
docker --version
docker ps
```

Install kubectl:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo install kubectl /usr/local/bin/kubectl
```

Install Minikube:

```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

An EC2 instance needs enough CPU, memory, and disk space for both the host and the Minikube cluster. The Docker driver requires a working Docker installation and user access to Docker.

## 2. Start and inspect the cluster

On macOS, Windows, and Linux with Docker available:

```bash
minikube start --driver=docker --cpus=2 --memory=4096
minikube status
kubectl cluster-info
kubectl get nodes -o wide
```

Check that the node reaches `Ready`:

```bash
kubectl get nodes
```

## 3. Deploy NGINX with YAML

Create `deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 2
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

Apply the manifest and check the Deployment and Pods:

```bash
kubectl apply -f deployment.yaml
kubectl get deployments
kubectl get pods -o wide
```

## 4. Expose NGINX with a NodePort Service

Create `service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  type: NodePort
  selector:
    app: nginx
  ports:
    - name: http
      port: 80
      targetPort: 80
      nodePort: 30007
```

Apply it and inspect the Service:

```bash
kubectl apply -f service.yaml
kubectl get service nginx-service
```

Open the application:

```bash
minikube service nginx-service
```

To print the URL instead:

```bash
minikube service nginx-service --url
```

On Docker Desktop, the terminal may need to stay open while Minikube runs the service tunnel.

## 5. Practice with kubectl

### Pods

```bash
kubectl run mypod --image=nginx:stable
kubectl get pods -o wide
kubectl describe pod mypod
kubectl logs mypod
kubectl exec -it mypod -- /bin/sh
kubectl delete pod mypod
```

### Deployments and scaling

```bash
kubectl create deployment myapp --image=nginx:stable
kubectl get deployments
kubectl get pods

kubectl scale deployment myapp --replicas=3
kubectl get pods -o wide
kubectl describe deployment myapp
```

### Services

Expose `myapp`:

```bash
kubectl expose deployment myapp \
  --name=myapp-service \
  --type=NodePort \
  --port=80 \
  --target-port=80

kubectl get services
minikube service myapp-service --url
```

Practice other Service types:

```bash
kubectl expose deployment nginx-deployment \
  --name=nginx-clusterip \
  --type=ClusterIP \
  --port=80

kubectl expose deployment nginx-deployment \
  --name=nginx-loadbalancer \
  --type=LoadBalancer \
  --port=80

kubectl get services
```

For Minikube LoadBalancer testing, run this in a separate terminal:

```bash
minikube tunnel
```

### Rolling updates and rollback

The `myapp` Deployment created above uses `nginx` as its container name.

```bash
kubectl set image deployment/myapp nginx=nginx:1.27
kubectl rollout status deployment/myapp
kubectl rollout history deployment/myapp
kubectl rollout undo deployment/myapp
```

### Namespaces

```bash
kubectl create namespace dev
kubectl create deployment webapp --image=nginx:stable -n dev
kubectl get pods -n dev
kubectl get all -n dev

kubectl delete namespace dev
```

## 6. Optional Minikube add-ons

List available add-ons:

```bash
minikube addons list
```

Enable metrics, ingress, and the dashboard:

```bash
minikube addons enable metrics-server
minikube addons enable ingress
minikube addons enable dashboard
```

View resource metrics after they become available:

```bash
kubectl top nodes
kubectl top pods
```

Open the dashboard:

```bash
minikube dashboard
```

## 7. Troubleshooting

```bash
kubectl get pods -A
kubectl get events --sort-by=.metadata.creationTimestamp
kubectl describe pod <pod-name>
kubectl logs <pod-name>
kubectl get services
kubectl get endpointslices
kubectl get nodes
kubectl cluster-info
minikube logs
```

If a Pod is not ready, inspect its description and events first:

```bash
kubectl describe pod <pod-name>
```

## 8. Cluster management

```bash
minikube status
minikube ip
minikube profile list
minikube ssh

minikube stop
minikube start
```

Delete the cluster when you no longer need it:

```bash
minikube delete
```

## 9. Cleanup the NGINX lab

From the directory containing the YAML files:

```bash
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml
```

Stop Minikube:

```bash
minikube stop
```

## Key concepts

- **Pod:** Runs one or more containers.
- **Deployment:** Manages replicated Pods and supports updates and rollbacks.
- **Service:** Provides a stable network endpoint for selected Pods.
- **NodePort:** Exposes a Service on a port reachable through the Minikube node.
- **Minikube:** Runs a Kubernetes cluster for local learning and testing.

## License

This project is released under the [MIT License](LICENSE).

## Author

**Atul Kamble**  
Cloud Solutions Architect | DevOps Trainer

- [LinkedIn](https://www.linkedin.com/in/atuljkamble/)
- [GitHub](https://github.com/atulkamble)
- [X](https://x.com/Atul_Kamble)
- [Instagram](https://www.instagram.com/atuljkamble/)
- [Website](https://atulkamble.com/)

## Contributing

Suggestions and contributions are welcome. Open an issue or submit a pull request to improve the lab.
