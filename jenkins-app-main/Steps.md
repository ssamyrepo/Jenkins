
### Steps to Build the CI/CD Pipeline  

#### **Step 1: Set Up Jenkins Master and Agent**  
1. **Create Jenkins Master VM on AWS**:  
   - Launch an Ubuntu instance on AWS.  
   - Name it "Jenkins Master," select a free-tier instance type (e.g., t2.medium), and allocate 50 GB of storage.  
   - Generate a new key pair and launch the instance.  

2. **Install Java and Jenkins**:  
   - Update the system and install Java:  
     ```bash
     sudo apt update  
     sudo apt install openjdk-17-jdk  
     ```  
   - Install Jenkins:  
     ```bash
     wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -  
     sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'  
     sudo apt update  
     sudo apt install jenkins  
     sudo systemctl start jenkins  
     sudo systemctl enable jenkins  
     ```  
   - Verify Jenkins is running:  
     ```bash
     systemctl status jenkins  
     ```  

3. **Create Jenkins Agent VM on AWS**:  
   - Launch another Ubuntu instance named "Jenkins Agent."  
   - Install Java and Docker:  
     ```bash
     sudo apt install docker.io  
     sudo usermod -aG docker $USER  
     ```  
   - Reboot the system to apply changes.  

4. **Set Up SSH Key-Based Authentication**:  
   - Generate an SSH key on the Jenkins Master:  
     ```bash
     ssh-keygen  
     ```  
   - Copy the public key to the Jenkins Agent’s `authorized_keys` file.  

5. **Configure Jenkins Agent in Jenkins UI**:  
   - Go to "Manage Jenkins" > "Nodes" > "New Node."  
   - Name the node "Jenkins Agent," set the remote root directory to `/home/ubuntu`, and configure SSH credentials using the private key.  
   - Save the configuration and verify the connection.  

---

#### **Step 2: Set Up the CI Pipeline**  
1. **Create a Jenkins Pipeline Job**:  
   - Name the job "Register-App-CI."  
   - Configure it to pull the Jenkinsfile from your GitHub repository.  

2. **Define Pipeline Stages in Jenkinsfile**:  
   - **Clean Workspace**: Clean the workspace before starting the build.  
   - **Checkout Code**: Pull the code from the GitHub repository.  
   - **Build Application**: Build the application using Maven.  
   - **Test Application**: Run unit tests using Maven.  
   - **SonarQube Analysis**: Perform static code analysis using SonarQube.  
   - **Build and Push Docker Image**: Build a Docker image and push it to Docker Hub.  
   - **Trivy Scan**: Scan the Docker image for vulnerabilities.  
   - **Cleanup Artifacts**: Clean up the workspace after the build.  

---

#### **Step 3: Set Up SonarQube**  
1. **Launch SonarQube Server on AWS**:  
   - Launch an Ubuntu instance and install PostgreSQL.  
   - Configure PostgreSQL for SonarQube.  

2. **Install Java and SonarQube**:  
   - Install Java:  
     ```bash
     sudo apt install openjdk-17-jdk  
     ```  
   - Download and install SonarQube:  
     ```bash
     sudo wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.6.1.59531.zip  
     sudo unzip sonarqube-9.6.1.59531.zip  
     ```  
   - Configure SonarQube properties with database credentials.  

3. **Start SonarQube Service**:  
   ```bash
   sudo systemctl start sonarqube  
   sudo systemctl enable sonarqube  
   ```  

4. **Integrate SonarQube with Jenkins**:  
   - Generate a token in SonarQube and add it as a credential in Jenkins.  
   - Install the SonarQube Scanner plugin in Jenkins and configure it.  
   - Add the SonarQube analysis stage to the Jenkinsfile.  

---

#### **Step 4: Set Up EKS and ArgoCD**  
1. **Create an EKS Cluster**:  
   - Install `eksctl` and create a cluster:  
     ```bash
     eksctl create cluster --name my-cluster --region us-east-1 --nodegroup-name my-nodes --node-type t2.small --nodes 3  
     ```  

2. **Install ArgoCD on EKS**:  
   - Create a namespace for ArgoCD:  
     ```bash
     kubectl create namespace argocd  
     ```  
   - Apply ArgoCD manifests:  
     ```bash
     kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml  
     ```  
   - Expose ArgoCD via a LoadBalancer:  
     ```bash
     kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'  
     ```  

3. **Log in to ArgoCD and Add EKS Cluster**:  
   - Extract the ArgoCD password:  
     ```bash
     kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d  
     ```  
   - Log in to ArgoCD via the browser and add the EKS cluster.  

4. **Connect GitOps Repository to ArgoCD**:  
   - Add your GitOps repository (containing Kubernetes manifests) to ArgoCD.  

---

#### **Step 5: Set Up the CD Pipeline**  
1. **Create a CD Job in Jenkins**:  
   - Name the job "Register-App-CD."  
   - Configure it to update the deployment YAML file in the GitOps repository with the new Docker image tag.  

2. **Automate CD Job Triggering**:  
   - Trigger the CD job automatically upon successful completion of the CI job.  

---

#### **Step 6: Test the Pipeline**  
1. **Make Changes to Application Code**:  
   - Push changes to the GitHub repository.  

2. **Verify Pipeline Execution**:  
   - Ensure the CI job triggers automatically, followed by the CD job.  
   - Confirm that the changes are deployed to the EKS cluster via ArgoCD.  

---
