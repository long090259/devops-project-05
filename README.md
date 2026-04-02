# 📋 PROJECT: FULL-STACK WEBSITE DEPLOYMENT ON K8S WITH CI/CD PIPELINE


---

## 1. Project Description

This project simulates the deployment of an e-commerce website (full-stack), using a Kubernetes-based infrastructure.

The core objective is to ensure the system meets the following criteria:

- **Automation:** Automatically build and deploy when new source code is pushed.  
- **Scalability:** Automatically scale pods when resources are overloaded.  
- **High Availability:** Ensure system components are fault-tolerant and data is preserved in case of failures.  
- **Security:** Sensitive information such as passwords, accounts, tokens, etc. are encrypted or securely stored.  

---

## 2. Architecture and Components
<img width="2048" height="1725" alt="image" src="https://github.com/user-attachments/assets/ae23bd41-9e8e-411c-9d8f-082741bf0c0c" />

### Source Code

- **Backend:** Developed using Java Spring Boot, built with Maven and run on Java.  
  Runs on port 8080 and provides REST APIs for the frontend.

- **Frontend:** Developed using Angular framework, built with Node.js and served via Nginx on port 80 for optimized performance.

- **Database:** Uses MariaDB.

---

### System Components

- **External Load Balancer:** Nginx acts as the single gateway, distributing HTTP requests from clients to Kubernetes nodes.

- **Kubernetes Cluster:** Consists of 3 nodes, each acting as Master/Worker, running Ubuntu 24.04. Managed centrally via Rancher.

- **Ingress:** Uses NGINX Ingress Controller to route traffic based on domain to frontend and backend services.

- **Storage:** Uses NFS Server as centralized persistent storage to ensure data is not lost when pods restart.

- **ConfigMap:** Manages the `application.properties` configuration of the backend.

- **Secret:** Stores sensitive information such as MariaDB credentials (account, port, host, etc.).

- **HPA:** Automatically scales pods when CPU usage exceeds 80%.

- **Git Repository:** GitLab

- **CI/CD Pipeline:** Uses GitLab CI/CD to automatically build, push images, and deploy to Kubernetes when a version tag is created.

- **Registry:** Docker Hub

---

## 3. Working Flow
<img width="2048" height="784" alt="image" src="https://github.com/user-attachments/assets/3a35d7bc-b8a2-421f-b5c9-b6f6944f745c" />

1. Client accesses the website `devopslab.com` or `api.devopslab.com`  
2. DNS resolves to the IP address of Nginx Reverse Proxy: `192.168.1.57`  
3. Nginx receives HTTP request and forwards it to NodePort `30080` on Kubernetes nodes  
4. Service forwards the request to Nginx Ingress Controller pod  
5. Nginx Ingress Controller performs routing as follows:  
   - `devopslab.com` → frontend service (port 80)  
   - `api.devopslab.com` → backend service (port 8080)  
6. Frontend and backend services route traffic to corresponding pods  

---

## 4. CI/CD Flow
<img width="2031" height="2177" alt="image" src="https://github.com/user-attachments/assets/2ecaaf25-ac48-4f49-9bc8-7b661a318849" />

1. Developer pushes new source code to the **Dev branch** on GitLab  
2. Developer creates a merge request from Dev to Pro branch  
3. The merge request is approved and a version tag is created  
4. When a tag is created on the Pro branch, GitLab CI/CD triggers the pipeline  
5. GitLab Runner builds and pushes Docker images to Docker Hub  
6. GitLab Runner sends commands to Kubernetes API Server to update deployment images  
7. Container runtime pulls new images and deploys new pods to replace old ones  

Because the deployment uses **rolling update**, there is no downtime during the update process.

Sensitive information such as Docker Hub User, Docker Hub Access Tokens, and Kubeconfig are stored as variables in GitLab to ensure security.

---
