# 🛡️ DevSecOps Homelab: Secure CI/CD Pipeline

This repository contains the source code, Docker configuration, and automated CI/CD pipeline for a secure, containerized web application. It serves as a proof-of-concept for enterprise-grade DevSecOps practices, featuring automated vulnerability scanning and GitOps deployment strategies.

## 🏗️ Architecture & Tech Stack

* **Application:** Nginx (Serving static HTML)
* **Containerization:** Docker (Alpine Linux base image)
* **CI/CD Automation:** GitHub Actions
* **Security Scanning:** Trivy by Aqua Security
* **Container Registry:** GitHub Container Registry (GHCR)
* **Orchestration (Local):** Kubernetes (K3s)

## 🔄 The CI/CD Pipeline

This project utilizes a GitHub Actions workflow (`.github/workflows/build-push.yml`) triggered automatically on every push to the `main` branch. 

The pipeline enforces a strict security gate before any code reaches the registry:

1. **Build (Local Stage):** The Docker image is built locally on the GitHub Action runner.
2. **Security Audit:** Trivy scans the newly built image for OS-level and library vulnerabilities (`CRITICAL` and `HIGH` severity).
3. **Quality Gate:** * If vulnerabilities are detected, the pipeline fails (Exit Code 1), and the deployment is blocked.
   * If the scan passes, the pipeline proceeds to step 4.
4. **Publish:** The secure image is tagged and pushed to GHCR for deployment.

## 🚀 Deployment Instructions

To deploy this application to your local Kubernetes cluster:

**1. Clone the repository:**
```bash
git clone [https://github.com/YOUR_GITHUB_USERNAME/sample-app.git](https://github.com/YOUR_GITHUB_USERNAME/sample-app.git)
cd sample-app








2. Ensure K3s is running and configured:
Verify your local Kubernetes context is set properly.

Bash
kubectl get nodes
3. Apply the Kubernetes manifest:
This will spin up a Deployment (1 replica) and expose it via a NodePort Service.

Bash
kubectl apply -f deploy.yml
4. Access the Application:
Retrieve the assigned NodePort:

Bash
kubectl get svc sample-app-service
Navigate to http://<YOUR_NODE_IP>:<ASSIGNED_PORT> in your web browser.

🔒 Security Remediation History
[CVE-2026-6732] libxml2 Vulnerability: Caught by Trivy automated scan during the build phase. Remediated by injecting RUN apk update && apk upgrade into the Dockerfile to pull the patched 2.13.9-r1 library before deployment.
