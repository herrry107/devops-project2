# devops-project2
Jenkins Project with Kubernetes Integration

# Setup Jenkins Master & Agent

create 2 ec2 instance on cloud and change hostname of both master and agent and run same command on both server
<pre><code>
vi /etc/hostname
apt update
apt upgrade
</code></pre>

Install Jenkins on both System by [official jenkins page](https://www.jenkins.io/doc/book/installing/) or follow below commands

install Java first

<pre><code>
sudo apt update
sudo apt install fontconfig openjdk-21-jre
java -version
</code></pre>

<pre><code>
  sudo wget -O /etc/apt/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.asc]" \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt update
sudo apt install jenkins
</code></pre>

<pre><code>
  systemctl enable jenkins
  systemctl start jenkins
</code></pre>

allow port 8080 in incoming in aws security group

now add ssh-key, run command on master node
<pre><code>ssh-keygen</code></pre>

copy /.ssh/id_rsa.pub key data from Jenkins-Master and paste into Jenkins-Agent /.ssh/authorized_keys

now uncomment 2 lines from /etc/ssh/sshd_config
<pre><code>
  PublickeyAuthentication yes
  AuthorizedKeysFile  .ssh/authorized_keys .ssh/authorized_keys2
</code></pre>

![uncomment](https://github.com/herrry107/devops-project2/blob/main/images/uncomment1.png)

add jenkins agent node. Go to Manage > Nodes

![node](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-add-node1.png)

![node](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-add-node2.png)

![node](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-add-node3.png)

![node](https://github.com/herrry107/devops-project2/blob/main/images/jenkins-add-node4.png)



open some jenkins plugin 
1) Maven Integrations
2) Pipeline Maven Integration
3) Eclipse Termurin installer

![uncomment](https://github.com/herrry107/devops-project2/blob/main/images/pluginimage2.png)


add maven  and JDK in Jenkins > Tools

![uncomment](https://github.com/herrry107/devops-project2/blob/main/images/tools-maven1.png)

![uncomment](https://github.com/herrry107/devops-project2/blob/main/images/tools-jdk1.png)

add github credentials

Go to Manage Jenkins > Credentials(Security) 

Go to your github profile copy username  and create personall access token by setting > Developer Setting > Personall Access Token > Tokens (Classic) 

![github](https://github.com/herrry107/devops-project2/blob/main/images/github-token-img1.png)

![github](https://github.com/herrry107/devops-project2/blob/main/images/credentials-github1.png)

now create jenkins pipeline and build now if build successfully triggered than now we will push it to dockerhub

