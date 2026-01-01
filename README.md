# Automated CI/CD Pipeline with Jenkins & Docker

This project demonstrates a fully automated Continuous Integration and Continuous Deployment (CI/CD) pipeline. It automates the process of building and deploying a containerized web application (Nginx) using Jenkins on a WSL2 environment.

## 🚀 Overview
The goal of this project is to implement a "push-to-deploy" workflow. When code is pushed to GitHub, Jenkins automatically triggers a build, creates a new Docker image, and deploys the updated container.

## 🛠 Tech Stack
* **Source Control:** GitHub
* **CI/CD Tool:** Jenkins (Pipeline as Code)
# Automated CI/CD Web Deployment Pipeline

This repository contains a fully automated CI/CD pipeline built to demonstrate the lifecycle of a modern web application. Using **Jenkins** and **Docker** on **WSL2**, the project automates the transition from code changes in GitHub to a live production-ready container.

## 🚀 Overview
The pipeline follows the "Infrastructure as Code" philosophy. Once a developer updates the code, Jenkins takes over to build, test, and redeploy the application without manual intervention.

> **Note:** The pipeline is designed to be triggered via GitHub Webhooks for continuous deployment (CD), ensuring that every 'push' to the main branch is immediately reflected in the live environment.

## 🛠 Tech Stack
* **Automation Server:** Jenkins (Pipeline-as-Code)
* **Containerization:** Docker (Engine-based on WSL2)
* **Base Image:** Nginx (Alpine-base for efficiency)
* **Environment:** Ubuntu on WSL2
* **Source Control:** GitHub

## 🏗 Pipeline Stages
1.  **Checkout:** Pulls the latest source code from the GitHub repository.
2.  **Check Tools:** Verifies the availability of the Docker CLI within the Jenkins environment.
3.  **Build & Deploy:** * Builds a new Docker Image with a custom tag.
    * Removes any previously running containers to prevent port conflicts.
    * Deploys the new containerized application.

## 🔌 Ports & Services
The project orchestrates multiple services, each communicating through specific ports:

| Port | Service | Description |
| :--- | :--- | :--- |
| **8080** | **Jenkins UI** | The management dashboard where the pipeline is configured and monitored. |
| **8081** | **Web Application** | The live production site (Nginx container) serving the `index.html`. |
| **50000** | **Jenkins Agent** | Communication port used for connecting Jenkins distributed build agents. |

## 📸 Project Showcase

### 1. Jenkins Stage View
![Jenkins Success](https://github.com/shakdouh/project-1-jenkins-docker-cicd/blob/main/screenshots/01-jenkins-pipeline.png)
*The visual representation of the successful CI/CD flow.*

### 2. Live Deployment
![Application UI](https://github.com/shakdouh/project-1-jenkins-docker-cicd/blob/main/screenshots/03-docker-build.png)
*The final website running inside a Docker container on port 8081.*

## 📋 Local Setup (WSL2)
1.  Ensure **Docker Engine** is installed and running in your WSL2.
2.  Grant permissions to the Docker socket: `sudo chmod 666 /var/run/docker.sock`.
3.  Run Jenkins with Docker integration:
    ```bash
    docker run -d -p 8080:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -u root jenkins/jenkins:lts
    ```
4.  Install Docker CLI inside the Jenkins container:
    ```bash
    docker exec -u root jenkins apt-get update && apt-get install -y docker.io
    ```

## 💡 Key Learnings
* Implementing **Docker-outside-of-Docker (DooD)** to allow Jenkins to manage host containers.
* Mastering **Groovy-based Jenkinsfiles** for declarative automation.
* Understanding **Network Mapping** and port forwarding between Host and Containers.

---
	
Created by [Hesham Shakdouh] - Feel free to connect on [www.linkedin.com/in/hesham-shakdouh-87bb57201].
