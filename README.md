Complete CI/CD Pipeline Documentation
Jenkins + Docker + Kubernetes (GKE)
This document explains the complete CI/CD pipeline project created using Jenkins, Docker, DockerHub, Kubernetes
(GKE), GitHub Webhooks, and AWS EC2. The pipeline automatically builds, pushes, and deploys the application
whenever code is pushed to GitHub.
1. Project Architecture
Developer Pushes Code to GitHub ↓ GitHub Webhook Triggers Jenkins ↓ Jenkins Pipeline Executes ↓ Code Cloned
on Docker-Agent EC2 ↓ Docker Image Build ↓ Docker Image Push to DockerHub ↓ Deploy to Kubernetes GKE
Cluster ↓ Application Available Publicly
2. Tools and Technologies Used
• GitHub – Source code repository
• Jenkins – Continuous Integration and Continuous Deployment automation
• Docker – Containerization platform
• DockerHub – Docker image registry
• Kubernetes (GKE) – Container orchestration
• AWS EC2 – Jenkins Docker build agent
• Ngrok – Public tunnel for local Jenkins webhook
• MobaXterm – SSH terminal management
3. Jenkins Local Setup
Jenkins was installed locally on Windows. Java was installed first because Jenkins requires Java runtime. The
Jenkins server runs on:
http://localhost:8080
Important Jenkins plugins installed:
• Git Plugin
• Docker Pipeline Plugin
• SSH Build Agents Plugin
• Credentials Binding Plugin
• Kubernetes CLI Plugin
4. EC2 Docker Agent Setup
An Ubuntu EC2 instance was launched in AWS. This instance acts as Jenkins remote build agent.
Important setup commands:
5. Docker Installation Commands
sudo apt update
sudo apt install docker.io -y
sudo systemctl start docker
sudo systemctl enable docker
docker --version
6. Git Installation on EC2
Git was installed inside the EC2 agent because Jenkins needs Git for repository cloning.
7. Git Installation Commands
sudo apt update
sudo apt install git -y
git --version
which git
8. Jenkins Agent Configuration
Jenkins Node configuration:
<br/><br/>
Node Name: docker-agent<br/>
Remote Root Directory: /home/ubuntu/jenkins<br/>
Labels: docker-agent<br/>
Launch Method: Launch agents via SSH
<br/><br/>
SSH private PEM key from EC2 was added into Jenkins credentials.
9. GitHub Webhook Configuration
Ngrok was used because Jenkins runs locally on Windows and GitHub needs a public URL.
Ngrok command:
10. Ngrok Command
ngrok http 8080
11. GitHub Webhook URL
The webhook URL added in GitHub repository:
https://YOUR-NGROK-URL/github-webhook/
Webhook Event:
Just the push event
12. DockerHub Credentials Setup
DockerHub credentials were added in Jenkins:
<br/><br/>
Manage Jenkins → Credentials → Global Credentials
<br/><br/>
Credential Type:
Username with password
<br/><br/>
Credential ID:
dockerhub-creds
13. GKE Cluster Setup
Google Kubernetes Engine cluster was created using Google Cloud SDK.
14. GKE Cluster Creation Command
gcloud container clusters create cluster-jenkins --zone us-central1-c --num-nodes 2
15. Service Account Setup
A GCP service account was created for Jenkins authentication. Required roles assigned:
• Kubernetes Engine Admin
• Storage Admin
16. Service Account Commands
gcloud iam service-accounts create jenkins-gke-sa
gcloud projects add-iam-policy-binding jenkins-doc --member="serviceAccount:jenkins-gke-sa@jenkins-doc.iam.gser
17. Service Account Key Generation
gcloud iam service-accounts keys create jenkins-gke-key.json --iam-account=jenkins-gke-sa@jenkins-doc.iam.gserv18. Kubernetes Deployment YAML Explanation
The deployment.yaml file creates Kubernetes pods.
Important configurations:
• replicas: 2 → Creates 2 pods
• selector → Identifies pod labels
• containerPort: 5000 → Flask application port
• image → DockerHub image
19. Kubernetes Service YAML Explanation
service.yaml exposes the application publicly.
Service Type: LoadBalancer
The LoadBalancer automatically creates a public IP address.
20. Jenkinsfile Deep Explanation
The Jenkinsfile is written using Groovy Declarative Pipeline syntax.
Pipeline Stages:
1. Clone Repo
2. Build Docker Image
3. Push Docker Image to DockerHub
4. Deploy to GKE
21. Clone Repo Stage
deleteDir() removes previous workspace files.
<br/><br/>
git clone command downloads latest code from GitHub.
22. Build Docker Image Stage
Docker image build command:
<br/><br/>
docker build -t IMAGE_NAME:TAG .
<br/><br/>
Docker image tagging:
<br/><br/>
docker tag IMAGE_NAME:TAG IMAGE_NAME:latest
23. Push to DockerHub Stage
Jenkins securely accesses DockerHub credentials using withCredentials.
<br/><br/>
docker login authenticates DockerHub access.
<br/><br/>
docker push uploads image to DockerHub repository.
24. Deploy to GKE Stage
The GCP service account JSON key is loaded securely from Jenkins credentials.
<br/><br/>
Important deployment commands:
<br/>
• gcloud auth activate-service-account<br/>
• gcloud container clusters get-credentials<br/>
• kubectl apply<br/>
• kubectl set image<br/>
• kubectl rollout status
25. Kubernetes Rolling Update
kubectl set image updates the deployment image version.
<br/><br/>
Kubernetes gradually replaces old pods with new pods without downtime.
This process is called Rolling Update.
26. Pipeline Automation Flow
Whenever developer pushes code:
1. GitHub webhook triggers Jenkins automatically
2. Jenkins starts pipeline
3. Docker image builds automatically
4. Docker image pushes to DockerHub
5. Kubernetes deployment updates automatically
27. Problems Faced During Setup
• SSH connection timeout issues
• Jenkins agent Git path mismatch
• Git not installed in EC2 agent
• Docker permission issues
• GKE authentication failures
• Webhook connectivity problems
28. Solutions Implemented
• Opened EC2 security group port 22
• Installed Git inside Ubuntu EC2
• Corrected Jenkins Git executable path
• Added DockerHub credentials securely
• Re-created deleted GKE cluster and service account
29. Final Successful Deployment
Final deployment was successfully completed. The application became publicly accessible through Kubernetes
LoadBalancer External IP.
Pipeline Status: SUCCESS
30. Key Learning Outcomes
• CI/CD Pipeline Automation
• Jenkins Pipeline as Code
• Docker Image Management
• Kubernetes Deployment and Services
• GitHub Webhook Integration
• GKE Cluster Management
• Rolling Updates in Kubernetes
• Jenkins Distributed Build Architecture
This project demonstrates a complete real-world DevOps CI/CD implementation using cloud infrastructure,
automation tools, and Kubernetes deployment strategies.
