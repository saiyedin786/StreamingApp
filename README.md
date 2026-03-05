
Problem statement: 
Graded Project on Orchestration and Scaling
Jenkins Access Details
URL: https://jenkinsacademics.herovired.com/
Username: herovired
Password: herovired
Project Repository
Main Repository: https://github.com/UnpredictablePrashant/StreamingApp.git
Instructions to Sync Fork with Upstream: Pull updates from original GitHub repository into a fork
Project Workflow
Step 1: Version Control with Git
Fork the main repository into your GitHub account.
Maintain your version by syncing updates from the main repository as needed.
Step 2: Prepare the MERN Application
Containerize the Application
Create Dockerfiles for each component (Frontend and Backend).Push Docker Images to Amazon ECR
Build Docker images for both components.Create individual ECR repositories.
Push the images to their respective ECR repositories.
Step 3: AWS Environment Setup
Install and Configure AWS CLI
Set up AWS CLI with your access credentials.
Step 4: Continuous Integration (CI) using Jenkins
Set Up Jenkins
Install Jenkins on an AWS EC2 instance.
Configure necessary plugins and credentials.Create Jenkins Pipelines
Build and push Docker images to ECR using Jenkins jobs.
Trigger builds automatically on new commits from the Git repository.
Step 5: Kubernetes Deployment (EKS)
Create an EKS Cluster
Use eksctl or similar tools to set up Amazon EKS.Deploy via Helm
Package and deploy the MERN stack using Helm charts.
Step 6: Monitoring and Logging
Set Up Monitoring
Use Amazon CloudWatch for metrics and alarms.Configure Logging
Centralize application logs using CloudWatch Logs or similar tools.
Step 7: Documentation
Document the complete system architecture and deployment process.
Include diagrams, configuration steps, and scripts.
Upload the documentation to your GitHub repository.
Step 8: Final Validation
Verify that both frontend and backend are functional and accessible.
Ensure the application meets deployment and scaling requirements.
Bonus: Step 9 — ChatOps Integration
Create SNS Topics
Use SNS topics for various events (e.g., deployment success/failure).Integrate with Messaging Platform
Connect SNS with Slack, Microsoft Teams, or Telegram for real-time alerts.
________________________________________
Complete Implementation Guide (Step-by-Step)
Step 1: Launch an ec2 instance Fork and Sync the Repository
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/5dfd1290-05d5-410f-a5de-b66dbf18b36c" />

Forking the Repo
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/21602b5c-5ab3-49bc-8b8d-e29743c3469b" />

git clone https://github.com/<your-username>/StreamingApp.git
cd StreamingApp
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/52e08db2-48e9-4b8d-aa17-dd8df9a9f0e2" />

Add upstream repository
git remote add upstream https://github.com/UnpredictablePrashant/StreamingApp.git

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/6ac82b02-1913-4b23-8cf0-223a750c14df" />

Sync fork with upstream
git fetch upstream
git checkout main
git merge upstream/main
git push origin main



Step 2: Docker installation
1.	Docker installation on ec2
🔹 Step 1: Remove old Docker (if any)
sudo apt remove -y docker docker.io containerd runc

🔹 Step 2: Install prerequisites
sudo apt update
sudo apt install -y ca-certificates curl gnupg

🔹 Step 3: Add Docker’s official GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

🔹 Step 4: Add Docker repository (IMPORTANT)
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null



🔹 Step 5: Install Docker + Compose plugin
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

🔹 Step 6: Verify
docker --version
docker compose version
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/08f6a2cb-3002-4940-a070-36251f6f5db4" />
🔹 Step 7 (Optional but recommended): Run Docker without sudo
sudo usermod -aG docker $USER
newgrp docker
Step 2: Jenkins installation

sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version

sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2026.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins
sudo systemctl enable jenkins

sudo systemctl restart jenkins
sudo systemctl status jenkins

sudo usermod -aG jenkins $USER
newgrp jenkins

sudo usermod -aG docker jenkins
sudo systemctl restart docker
sudo systemctl restart jenkins

Enable port 8080 in security groups
Now Jenkins can be accessed at http:<public-ip>:8080

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/980e8342-b6db-42cb-8270-7052844d9556" />
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/ccef6cb6-0369-4161-a6d8-70e17ab7e154" />
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/7ece953c-38af-44be-a371-bcb0acfda3ac" />
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/f0e1a659-71b1-44ea-ab85-e534b72db59f" />


Step 4: EKS cluster installation
### Step 1: IAM Configuration
- Create a user `ismail` with `AdministratorAccess`.
- Generated Security Credentials: Access Key and Secret Access Key.

### Step 2: Install AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
aws configure
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/daa52003-39ba-4fea-a73c-52896b5d0fdc" />
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/69bf7868-0cbf-4197-b3c6-a5360def60be" />

### Step 5: Install kubectl
curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short –client
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/97e40ea9-f741-4153-93de-21ca297c6afb" />
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/a9222341-e548-4a0d-83f3-18af9839274b" />
### Step 6: Install eksctl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/520f62ff-7e67-422e-bb52-fb80f287099f" />


### Step 7: Setup EKS Cluster
eksctl create cluster --name streamingapp-cluster --region us-east-1 --node-type t2.medium --nodes-min 2 --nodes-max 2
aws eks update-kubeconfig --region us-east-1 --name streamingapp-cluster
kubectl get nodes
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/30298260-8a2e-4351-9b4d-4887dc3a4c0d" />

Create IAM Role for Jenkins (Using AWS Console)
1.	Go to Amazon Web Services Console
2.	Open IAM
3.	Click Roles → Create Role
4.	Select AWS Service → EC2
5.	Click Next
Attach Policies
Attach these policies:
•	AmazonEKSClusterPolicy
•	AmazonEKSWorkerNodePolicy
•	AmazonEC2ContainerRegistryReadOnly
•	AmazonEKS_CNI_Policy
6.	Click Next
7.	Role name: Jenkins-eks-role
8.	Click Create Role

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/88022b1c-e2ab-48f2-a8f2-718fe8a57104" />

Create kube directory for jenkins
sudo mkdir -p /var/lib/jenkins/.kube

Copy kubeconfig
sudo cp ~/.kube/config /var/lib/jenkins/.kube/config
________________________________________
give permission
sudo chown -R jenkins:jenkins /var/lib/jenkins/.kube

Step 5 — Verify
sudo ls /var/lib/jenkins/.kube
You should see:
Config





Step: editing aws-auth
kubectl edit configmap aws-auth -n kube-system
Make sure it looks exactly like this:
data:
  mapRoles: |
    - rolearn: arn:aws:iam::254292659362:role/jenkins-eks-role
      username: jenkins
      groups:
        - system:masters
⚠ Very Important
The pipe | after mapRoles must exist.
Right now your file likely looks like:
mapRoles:
but it must be:
mapRoles: |
Otherwise Kubernetes does not parse the role mapping correctly.
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/90351c7e-dd7d-4c96-b845-e55d653c22ae" />

Testing:
Creating a streamingapp-deployment  job in Jenkins
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/422addda-7366-4a78-8271-ca2809beaf20" />
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/c8d8b550-1741-4305-b88c-22fc36393d18" />

Now Jenkins integrated with streamingapp-cluster







Step 3: Create ECR Repositories
Go to AWS Console → ECR
Create two repositories
Streamingapp/frontend:latest
streamingapp/backend:latest
Example ECR URL:
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/188115b2-6456-48fc-8f2d-0102fc36b362" />
Step 5: Login to ECR
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l0y6b8g4

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/f2eae778-1f38-482a-a5f3-2847e900dd31" />
Step 12: Install Helm
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
________________________________________

Step 12: Installins aws Load balancer

eksctl utils associate-iam-oidc-provider \
--region us-east-1 \
--cluster streamingapp-cluster \
--approve


curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/main/docs/install/iam_policy.json

aws iam create-policy \
--policy-name AWSLoadBalancerControllerIAMPolicyforeks \
--policy-document file://iam_policy.json


eksctl create iamserviceaccount \
--cluster streamingapp-cluster \
--namespace kube-system \
--name aws-load-balancer-controller \
--attach-policy-arn arn:aws:iam::254292659362:policy/AWSLoadBalancerControllerIAMPolicyforeks \
--approve

helm repo add eks https://aws.github.io/eks-charts
helm repo update


helm install aws-load-balancer-controller eks/aws-load-balancer-controller \
-n kube-system \
--set clusterName=streamingapp-cluster \
--set serviceAccount.create=false \
--set serviceAccount.name=aws-load-balancer-controller


Step : Domain name mapping to aws load balancer dns
Namecheap domain-name : ismail-profile.online
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/2ebb0841-465b-46f5-8f62-fedd38f6dbea" />

Aws loadbalancer:
Kubectl get ingress
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/3eba19e3-0ef1-4d46-b20a-ae897d310d19" />

Step 6: Build and Push Docker Images and run helm install commain in pipeline
Build Jenkins pipeline to build images and push them to ecr repositories

pipeline {
    agent any

    stages {
        stage('clone') {
            steps {
                echo "cloning from the git repo"
                git url:"https://github.com/saiyedin786/StreamingApp.git",branch:"main"
                echo "cloning finished"
            }
        }
        stage('Building frontend') {
            steps {
                sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l0y6b8g4"
                echo "Building the Frontend"
                sh "docker build -t public.ecr.aws/l0y6b8g4/streamingapp/frontend:latest ./frontend "
                echo "Building frontend  finished"
                echo "pushing image to ecr"
                sh "docker push public.ecr.aws/l0y6b8g4/streamingapp/frontend:latest "
            }
        }
        stage('Building adminservice') {
            steps {
                sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l0y6b8g4"
                echo "Building the adminservice"
                sh "docker build -t public.ecr.aws/l0y6b8g4/streamingapp/backend:adminservice ./backend/adminService "
                echo "Building adminservice  finished"
                echo "pushing image to ecr"
                sh "docker push public.ecr.aws/l0y6b8g4/streamingapp/backend:adminservice "
                echo "Building adminservice  finished"
            }
        }
        stage('Building authservice') {
            steps {
                sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l0y6b8g4"
                echo "Building the authservice"
                sh "docker build -t public.ecr.aws/l0y6b8g4/streamingapp/backend:authservice ./backend/authService "
                echo "Building authservice  finished"
                echo "pushing image to ecr"
                sh "docker push public.ecr.aws/l0y6b8g4/streamingapp/backend:authservice "
                echo "Building authservice  finished"
            }
        }
        stage('Building chatservice') {
            steps {
                sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l0y6b8g4"
                echo "Building the chatservice"
                sh "docker build -t public.ecr.aws/l0y6b8g4/streamingapp/backend:chatservice ./backend/chatService "
                echo "Building chatservice  finished"
                echo "pushing image to ecr"
                sh "docker push public.ecr.aws/l0y6b8g4/streamingapp/backend:chatservice "
                echo "Building chatservice  finished"
            }
        }
        stage('Building streamingservice') {
            steps {
                sh "aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/l0y6b8g4"
                echo "Building the streamingservice"
                sh "docker build -t public.ecr.aws/l0y6b8g4/streamingapp/backend:streamingservice ./backend/streamingService "
                echo "Building streamingservice  finished"
                echo "pushing image to ecr"
                sh "docker push public.ecr.aws/l0y6b8g4/streamingapp/backend:streamingservice "
                echo "Building chatservice  finished"
            }
        }
        stage('running helm install') {
    steps {
        echo "creating pods"
        
        sh "pwd"
        sh "helm uninstall streamingapp || true"
        sh "helm install streamingapp ./streamingapp"
        
        echo "pods creation finished"
    }
}
    }
}

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/d438c276-0085-4701-ab78-8801131fdb84" />
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/60184268-409b-4599-b103-91a184e20734" />

Output :
Kubectl get all
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/45822f32-5665-4ee7-8bb9-e0359ad9491d" />

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/95693a60-a342-4ca8-9e68-f1bfaad8556d" />
Accessing from browser:
http://ismail-profile.online
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/ec57ce6d-27e9-4972-b0ab-162883c61c10" />
Registration successful:
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/c99528de-72d0-4328-8abb-cc37d3ee1560" />

Login successful:
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/354b14f1-0acd-44f6-9ae1-002be883f42e" />

Step 15: Setup Monitoring
Use
👉 Amazon CloudWatch
Install CloudWatch agent
or
Enable Container Insights


ec2 => seccurity -> iam role click -> add cloudwatchgentserverpolicy

aws eks create-addon \
    --addon-name amazon-cloudwatch-observability \
    --cluster-name streamingapp-cluster \
    --region us-east-1

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/e9a32c2f-420f-4620-b28e-5c2a2ef37154" />
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/2411fd47-8113-40d8-8570-d848928b046f" />

Step 16: Logging
Send logs to CloudWatch
kubectl logs <pod-name>
Configure
CloudWatch Logs
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/2921ebcc-4c6b-4ac9-91f3-fbaec0d13ebb" />

Step 17: ChatOps (Bonus)
Create SNS Topic
👉 Amazon SNS
Create topic
deployment-notification
Subscribe
Email
Slack webhook
Telegram bot
Jenkins pipeline can trigger SNS after deployment.

To send deployment notifications from Jenkins → SNS → Email / Slack / Telegram, you need 3 parts:
1️⃣ Create an SNS Topic
2️⃣ Create subscriptions (Email / Webhook)
3️⃣ Trigger SNS from Jenkins pipeline
Step: 1:
Topic creation will generate arn as below
arn:aws:sns:us-east-1:254292659362:sendemail
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/df54692e-d883-47c9-879e-56d6b655dce8" />
Step:2 create subscription
<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/469a9d7e-e597-49e8-b7a8-44e5145a2cf8" />

Now add below in Jenkins
stage('Send Deployment Notification') {
    steps {
        sh '''
        aws sns publish \
        --region us-east-1 \
        --topic-arn arn:aws:sns:us-east-1:254292659362:sendemail \
        --message "Deployment Successful: Streaming App deployed to EKS"
        '''
    }
} 

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/fc5ea8ca-1b8c-4fcb-96a1-30bb923bbaef" />

<img width="940" height="529" alt="image" src="https://github.com/user-attachments/assets/5d54bb51-64c0-4d6a-a20d-24b9422d966f" />



































