# Real-Time DevOps Project: Deploy to Kubernetes Using Jenkins



## Project Workflow
1. **Developer Pushes Code to GitHub**: Triggers CI job in Jenkins.
2. **Jenkins CI Job**:
   - Pulls code from GitHub repository.
   - Builds and tests artifact using Maven.
   - Runs static code analysis with SonarQube.
   - Builds Docker image and pushes it to Docker Hub.
   - Scans Docker image using Trivy.
3. **Jenkins CD Job**:
   - Updates the build number in deployment YAML.
   - ArgoCD pulls updated manifest from GitHub.
   - Deploys application to Kubernetes (EKS).
   - Sends notifications via Slack or email.

## Prerequisites
- **AWS Services**: EC2, EKS, IAM, S3
- **Tools & Dependencies**:
  - Jenkins
  - Maven
  - Docker
  - SonarQube
  - Trivy
  - ArgoCD
  - kubectl
  - eksctl

![image](https://github.com/user-attachments/assets/189369b0-2755-4012-9496-cc7717e924cf)


## Setup Instructions
### 1. Set Up Jenkins Master Node on AWS
- Launch an EC2 instance (Ubuntu, t2.micro, 15GB disk).
- Install Java, Jenkins, and required dependencies.
- Configure inbound rules (port 8080) for Jenkins.
- Start Jenkins service and obtain the admin password.

### 2. Set Up Jenkins Agent Node
- Launch another EC2 instance (Ubuntu, t2.micro, 15GB disk).
- Install Java and Docker.
- Configure SSH authentication between master and agent.
- Add Jenkins agent node in Jenkins.

### 3. Configure Jenkins
- Install necessary plugins: Maven, Pipeline, Docker, SonarQube, Trivy.
- Add GitHub and Docker Hub credentials.
- Configure Jenkins global tool settings (Maven, JDK, SonarQube, etc.).

### 4. Set Up SonarQube for Code Analysis
- Launch EC2 instance (t3.medium, 15GB disk) for SonarQube.
- Install PostgreSQL, Java, and SonarQube.
- Configure SonarQube properties and create service.
- Obtain API token for Jenkins integration.

### 5. Configure Docker Integration
- Install Docker plugins on Jenkins.
- Add Docker Hub credentials.
- Define environment variables in the Jenkinsfile.

### 6. Build and Push Docker Image
- Create a pipeline stage to build and push the Docker image.
- Verify image availability in Docker Hub.

### 7. Implement Trivy Security Scan
- Add a Jenkins pipeline stage for Trivy image scanning.
- Ensure all security vulnerabilities are reported.

### 8. Deploy to Kubernetes with ArgoCD
- Set up an EKS cluster using eksctl.
- Install and configure ArgoCD.
- Connect ArgoCD to GitHub repository containing Kubernetes manifests.
- Deploy applications automatically with ArgoCD.

### 9. Automate Deployment Using Jenkins CD Job
- Add a stage to update Kubernetes deployment manifest in GitHub.
- Trigger deployment on EKS via ArgoCD.

### 10. Validate the CI/CD Pipeline
- Push changes to GitHub and monitor the pipeline execution.
- Verify deployment on Kubernetes and application accessibility.


