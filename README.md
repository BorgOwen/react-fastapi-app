
---

````markdown
# React + FastAPI Full Stack Deployment on AWS

This project demonstrates the deployment of a full-stack web application built with **React (frontend)** and **FastAPI (backend)** using **Docker**, **Amazon ECR**, and **Amazon EC2**.

Both services are containerized, pushed to AWS ECR (Elastic Container Registry), and deployed on an EC2 instance using Docker Compose.

---

## 📁 Project Overview

**Goal:**  
Deploy a containerized frontend and backend application to an AWS EC2 instance using Docker Compose, pulling images directly from AWS ECR.

**Tech Stack:**
- Frontend: React (JavaScript)
- Backend: FastAPI (Python)
- Containerization: Docker & Docker Compose
- Cloud Platform: Amazon Web Services (AWS)
  - Elastic Container Registry (ECR)
  - Elastic Compute Cloud (EC2)
- OS: Amazon Linux 2

---

## 🏗️ Steps to Deploy

### 1. Clone the Repository
```bash
git clone https://github.com/BorgOwen/react-fastapi-app.git
cd react-fastapi-app
````

---

### 2. Build Docker Images Locally (Create Dockerfiles for both apps)

#### Backend (FastAPI)

```bash
docker build -t fastapi-backend ./FASTAPI
```

#### Frontend (React)

```bash
docker build -t react-frontend ./React
```

---

### 3. Create ECR Repositories

In the AWS Console or CLI:

```bash
aws ecr create-repository --repository-name fastapi-backend --region eu-north-1
aws ecr create-repository --repository-name react-frontend --region eu-north-1
```

---

### 4. Authenticate Docker with ECR

#### Private ECR's

```bash
aws ecr get-login-password --region eu-north-1 \
| docker login --username AWS --password-stdin 784908116787.dkr.ecr.eu-north-1.amazonaws.com/fastapi-backend

aws ecr get-login-password --region eu-north-1 \
| docker login --username AWS --password-stdin 784908116787.dkr.ecr.eu-north-1.amazonaws.com/react-frontend
```

---

### 5. Tag and Push Docker Images

```bash
# Backend
docker tag fastapi-backend:latest 784908116787.dkr.ecr.eu-north-1.amazonaws.com/fastapi-backend:latest
docker push 784908116787.dkr.ecr.eu-north-1.amazonaws.com/fastapi-backend:latest

# Frontend
docker tag react-frontend:latest 784908116787.dkr.ecr.eu-north-1.amazonaws.com/react-frontend:latest
docker push 784908116787.dkr.ecr.eu-north-1.amazonaws.com/react-frontend:latest
```

---

### 6. Docker Compose Configuration

```yaml
version: "3"
services:
  backend:
    image: 784908116787.dkr.ecr.eu-north-1.amazonaws.com/fastapi-backend:latest
    container_name: fastapi-backend
    ports:
      - "8000:8000"

  frontend:
    image: 784908116787.dkr.ecr.eu-north-1.amazonaws.com/react-frontend
    container_name: react-frontend
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://<EC2_PUBLIC_IP>:8000
    depends_on:
      - backend
```

---

### 7. Deploy to EC2

1. **Launch an EC2 instance** (Amazon Linux 2)

2. **Allow inbound rules** for ports `22`, `3000`, and `8000`

3. **Install Docker and Docker Compose:**

   ```bash
   sudo yum update -y
   sudo yum install docker -y
   sudo systemctl start docker
   sudo usermod -aG docker ec2-user

   # Install Docker Compose (v2+)
   sudo curl -L "https://github.com/docker/compose/releases/download/v2.24.5/docker-compose-linux-x86_64" -o /usr/local/bin/docker-compose
   sudo chmod +x /usr/local/bin/docker-compose
   ```

4. **Copy the docker-compose.yml file** to your EC2 instance(EC2 instance connect):

   ```bash
   nano docker-compose.yml
   ```

5. **Run the containers:**

   ```bash
   docker compose up -d
   ```

---

### 8. Verify Deployment

Run:

```bash
docker ps
```

Expected:

```
fastapi-backend   Up 0.0.0.0:8000->8000/tcp
react-frontend    Up 0.0.0.0:3000->3000/tcp
```

Then visit:

* Frontend: [http://16.16.233.59:3000](http://16.16.233.59:3000)
* Backend: [http://16.16.233.59:8000/docs](http://16.16.233.59:8000/docs)

---

## 🔗 Useful Links

* **ECR Backend Image:** `784908116787.dkr.ecr.eu-north-1.amazonaws.com/fastapi-backend:latest`
* **ECR Frontend Image:** `784908116787.dkr.ecr.eu-north-1.amazonaws.com/react-frontend:latest`
* **Deployed Site:** [http://16.16.233.59:3000](http://16.16.233.59:3000)

---

## 🧾 Summary

This project showcases a standard **DevOps workflow**:

1. Build and containerize applications
2. Push to a container registry (ECR)
3. Deploy on a compute instance (EC2) using Docker Compose
4. Access deployed services via public endpoints

---

**Author:** Tolu Ogunwenmo
**Date:** November 2025
**Region:** eu-north-1
**Deployment:** AWS EC2 + ECR + Docker Compose

```

---

```
