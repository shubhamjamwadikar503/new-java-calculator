# new-java-calculator

# CI/CD Pipeline: GitHub → Jenkins → Docker → Minikube

This document provides step-by-step instructions to set up a complete CI/CD pipeline using:

- Git
- JDK 17
- Maven
- Docker
- Jenkins
- Minikube (Kubernetes)

---

## ⚙️ 1. Prerequisites

- EC2 Ubuntu 22.04 instance (t2.medium or larger recommended)
- Security Group must allow:
  - Port `22` (SSH)
  - Port `8080` (Jenkins UI)
  - Any custom `NodePort` (e.g., `30082`)
- SSH key (`.pem`) file to access EC2

---

## 🧰 2. Install Git, JDK, Maven, Docker

```bash
sudo apt update

# Git
sudo apt install -y git

# JDK 17
sudo apt install -y openjdk-17-jdk

# Maven
sudo apt install -y maven

# Docker
sudo apt install -y docker.io
sudo usermod -aG docker $USER
newgrp docker
```

## 🧰 3. Install Jenkins 
``` 
# Add Jenkins key and repo
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'

# Install Jenkins
sudo apt update
sudo apt install -y jenkins

# Start Jenkins
sudo systemctl enable jenkins
sudo systemctl start jenkins

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

```

## 🧰 4. Configure Docker for Jenkins
```
sudo usermod -aG docker jenkins
sudo systemctl restart jenkins
```

## 🧰 5. Install Minikube and Kubectl
```
# Kubectl
curl -LO "https://dl.k8s.io/release/$(curl -sL https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/

# Minikube
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Start Minikube with Docker driver
minikube start --driver=docker

```
## 🧰 6. Configure Kubeconfig for Jenkins
```
# Copy config to Jenkins home
sudo mkdir -p /var/lib/jenkins/.kube
sudo cp -r ~/.kube/config /var/lib/jenkins/.kube/config

# If Minikube uses certificates
sudo cp -r ~/.minikube /var/lib/jenkins/.minikube

# Fix ownership
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube
sudo chown -R jenkins:jenkins /var/lib/jenkins/.minikube
```
### 🧰 7. Kube-forward command

After deploying application on Minikube, Need to do port-forward
`kubectl port-forward --address 0.0.0.0 service/calculator-service 30082:8080`
