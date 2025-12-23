# 🚀 DevOps Project 2  
## Jenkins CI/CD Pipeline with Docker, AWS EKS & Argo CD

This project demonstrates a **complete end-to-end DevOps CI/CD pipeline** using modern DevOps tools and GitOps practices.

---

## 📌 Project Highlights

- Jenkins **Master–Agent architecture**
- GitHub **Webhook-based CI**
- Docker image build & push to **Docker Hub**
- Kubernetes deployment on **AWS EKS**
- GitOps-based delivery using **Argo CD**

---

## 🧱 Architecture Flow

**GitHub → Jenkins → Docker Hub → AWS EKS → Argo CD → Application**

Jenkins Project with Kubernetes Integration

# Setup Jenkins Master & Agent

create 2 ec2 instance on cloud and change hostname of both master and agent and run same command on both server
<pre><code>
vi /etc/hostname
apt update
apt upgrade
</code></pre>

### 1️⃣ Install Java (Required for Jenkins)

<pre><code>
sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version
</code></pre>

### 2️⃣ Install Jenkins on both System by [official jenkins page](https://www.jenkins.io/doc/book/installing/) or follow below commands

<pre><code>
  sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins
</code></pre>

Enable and start Jenkins:

<pre><code>
  systemctl enable jenkins
  systemctl start jenkins
</code></pre>

👉 Allow port 8080 in AWS Security Group

🔐 Step 2: Configure SSH Between Master & Agent

Generate SSH Key on Master

<pre><code>ssh-keygen</code></pre>

Copy Public Key

1) Copy ~/.ssh/id_rsa.pub from Master
2) Paste into Agent file:

copy /.ssh/id_rsa.pub key data from Jenkins-Master and paste into Jenkins-Agent /.ssh/authorized_keys

Update SSH Configuration on Agent
<pre><code>vi /etc/ssh/sshd_config</code></pre>

Uncomment these lines:

<pre><code>
  PublickeyAuthentication yes
  AuthorizedKeysFile  .ssh/authorized_keys .ssh/authorized_keys2
</code></pre>

![uncomment](https://github.com/herrry107/devops-project2/blob/main/images/uncomment1.png)

🖥️ Step 3: Add Jenkins Agent Node. Go to Manage > Nodes

![node](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-add-node1.png)

![node](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-add-node2.png)

![node](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-add-node3.png)

![node](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-add-node4.png)



🔌 Step 4: Install Required Jenkins Plugins
1) Maven Integrations
2) Pipeline Maven Integration
3) Eclipse Termurin installer

![uncomment](https://github.com/herrry107/devops-project2/blob/main/images/pluginimage2.png)

⚙️ Step 5: Configure Jenkins Tools

add maven  and JDK in Jenkins > Tools

![uncomment](https://github.com/herrry107/devops-project2/blob/main/images/tools-maven1.png)

![uncomment](https://github.com/herrry107/devops-project2/blob/main/images/tools-jdk1.png)

🔑 Step 6: Add GitHub Credentials

Go to GitHub → Settings → Developer Settings

Create Personal Access Token (Classic)

Go to Manage Jenkins > Credentials(Security) 


![github](https://github.com/herrry107/devops-project2/blob/main/images/github-token-img1.png)

![github](https://github.com/herrry107/devops-project2/blob/main/images/credentials-github1.png)

now create jenkins pipeline and build now if build successfully triggered than now we will push it to dockerhub

🐳 Step 7: Docker Integration, install more plugins for docker

1) Docker
2) Docker Commons
3) Docker Pipeline
4) Docker API
5) docker-build-step
6) CloudBees Docker Build and Publish

![docker](https://github.com/herrry107/devops-project2/blob/main/images/docker-plugin1.png)

![docker](https://github.com/herrry107/devops-project2/blob/main/images/docker-plugin2.png)

now Go to dockerhub and create personal access token

![docker](https://github.com/herrry107/devops-project2/blob/main/images/dockerhub1.png)

now go to jenkins > Manage > Credentials

![docker](https://github.com/herrry107/devops-project2/blob/main/images/dockerhub2.png)

🔁 Step 8: GitHub Webhook Setup (CI)

Add webhook in GitHub repository:
<pre><code>http://35.154.167.154:8080/github-webhook/</code></pre>

![github-webhook](https://github.com/herrry107/devops-project2/blob/main/images/github-webhook1.png)

![github-webhook](https://github.com/herrry107/devops-project2/blob/main/images/github-webhook2.png)

ensure to check  

☸️ Step 9: AWS EKS Setup + ArgoCD

install aws cli + kubectl + eksctl

**now create EKS Cluster by eksctl command**

<pre><code>eksctl create cluster --name demo-cluster --region ap-south-1</code></pre>

![eks](https://github.com/herrry107/devops-project2/blob/main/images/eks1.png)

**When delete Cluster by eksctl command**
<pre><code>eksctl delete cluster --name devops-project-cluster --region ap-south-1</code></pre>

🌐 Step 10: Install NGINX Ingress Controller

<pre><code>kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.11.1/deploy/static/provider/aws/deploy.yaml
</code></pre>


🚦 Step 11: Install Argo CD
<pre><code>
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
</code></pre>

Expose Argo CD UI

<pre><code>
kubectl patch svc argocd-server -n argocd -p '{"spec": {"type": "LoadBalancer"}}'
</code></pre>

**kubectl get svc argocd-server -n argocd**
<pre><code>
kubectl get svc argocd-server -n argocd
</code></pre>

Get Argo CD admin Password

<pre><code>
kubectl get secret -n argocd
kubectl edit secret argocd-initial-admin-secret -n argocd
</code></pre>

<pre><code>
echo <BASE64_PASSWORD> | base64 --decode
</code></pre>

now update password by goin to user info in argocd

📦 Step 12: Argo CD Application Setup

**now  create repository in argocd**

![repository](https://github.com/herrry107/devops-project2/blob/main/images/argocd1-add-repository.png)

**add app in  argocd**

![app](https://github.com/herrry107/devops-project2/blob/main/images/argocd-create-app1.png)

![app](https://github.com/herrry107/devops-project2/blob/main/images/argocd-create-app2.png)

**Now We can see  completely argocd pipeline synced**

![argocd-status](https://github.com/herrry107/devops-project2/blob/main/images/argocd2.png)

![argocd-status](https://github.com/herrry107/devops-project2/blob/main/images/argocd3.png)

![argocd-status](https://github.com/herrry107/devops-project2/blob/main/images/website1.png)

![jenkins-pipeline-complete](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-pipeline-complete.png)
