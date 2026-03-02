# 🐳 Django Application – Production-Style Containerized Deployment  
### Docker • Kubernetes (Minikube) • AWS EC2 • Terraform • DevOps

<p align="center">
  <img src="https://img.shields.io/badge/Docker-Containerized-blue?logo=docker" />
  <img src="https://img.shields.io/badge/Django-Web_App-green?logo=django" />
  <img src="https://img.shields.io/badge/Kubernetes-Orchestrated-blue?logo=kubernetes" />
  <img src="https://img.shields.io/badge/AWS-EC2-orange?logo=amazonaws" />
  <img src="https://img.shields.io/badge/IaC-Terraform-purple?logo=terraform" />
  <img src="https://img.shields.io/badge/Status-Production_Style-success" />
</p>

---

## 📌 Overview

This repository contains a Dockerized Django web application deployed in a production-style Kubernetes environment.

The project evolved in stages:

1. Containerized the Django application using Docker  
2. Deployed it on AWS EC2  
3. Provisioned infrastructure using Terraform  
4. Upgraded deployment to Kubernetes (Minikube cluster)  
5. Implemented scalable, observable, service-based architecture  

The goal was not just to run a container, but to deploy and manage an application the way it would be handled in a real-world DevOps setup.

---

## 🧱 Architecture

Application Layer:
- Django Web Application
- Docker Container
- DockerHub Image Registry

Kubernetes Layer (Minikube Cluster):
- Deployment (2 replicas)
- Service (ClusterIP → NodePort)
- Label-based service discovery
- Load balancing between pods

Infrastructure Layer:
- AWS EC2
- Terraform (Infrastructure as Code)

---

## 🚀 Implementation Details

### 1️⃣ Containerization

- Wrote a production-ready Dockerfile  
- Built the image locally  
- Tagged and pushed image to DockerHub  
- Verified portability across environments  

```bash
docker build -t ritvikkantip/python-web-app:v1 .
docker push ritvikkantip/python-web-app:v1
```

---

### 2️⃣ Kubernetes Deployment

Created a Deployment manifest to:

- Run 2 replicas  
- Enable rolling updates  
- Expose container port 8000  

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: python-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: sample-python-app
  template:
    metadata:
      labels:
        app: sample-python-app
    spec:
      containers:
      - name: sample-python-app
        image: ritvikkantip/python-web-app:v1
        ports:
        - containerPort: 8000
```

Apply deployment:

```bash
kubectl apply -f deployment.yml
```

---

### 3️⃣ Service Configuration

Created a Service to expose the application.

- Tested with ClusterIP (internal communication)
- Switched to NodePort for external access

```yaml
apiVersion: v1
kind: Service
metadata:
  name: python-deployment-service
spec:
  type: NodePort
  selector:
    app: sample-python-app
  ports:
    - port: 80
      targetPort: 8000
      nodePort: 30007
```

Apply service:

```bash
kubectl apply -f service.yml
```

Traffic Flow:

External Client → NodePort → Service → Pod

---

### 4️⃣ Debugging & Service Discovery

Encountered a real-world issue during deployment:

- NodePort was unreachable  
- `kubectl get endpoints` returned `<none>`  

Root cause:
Service selector did not match Pod labels.

Fix:
Aligned labels between Deployment and Service.

Useful debugging command:

```bash
kubectl get endpoints
```

---

### 5️⃣ Observability with Kubeshark

Used Kubeshark to:

- Monitor real-time Service-to-Pod communication  
- Observe load balancing across replicas  
- Validate traffic distribution  

This confirmed that requests were being distributed evenly across pods.

---

## 🛠 Tech Stack

- Django  
- Docker  
- DockerHub  
- Kubernetes (Minikube)  
- AWS EC2  
- Terraform  

---

## 🏗 Production Considerations

This deployment was tested on a Minikube cluster for development purposes.

For a real-world organizational setup:

- Provision multi-node cluster using KOPS
- Use LoadBalancer or Ingress Controller for external exposure
- Configure proper networking and security groups
- Integrate CI/CD pipelines
- Manage secrets and environment variables securely

---

## 🎯 Key Learnings

- Containerization ensures portability  
- Deployments enable scalability and rolling updates  
- Services rely strictly on label-selector matching  
- `kubectl get endpoints` is essential for debugging  
- Observability tools validate real traffic behavior  

---

## 📬 Conclusion

This project reflects a transition from:

Single Docker container  
→ Orchestrated Kubernetes workload  
→ Production-style deployment model  

It demonstrates practical implementation across containerization, orchestration, infrastructure automation, and traffic observability.
