


# Repo Refernece : https://github.com/Aj7Ay/Zomato-Clone.git
# Docker Image for refernce : docker pull mdsuh/zomato


# Steps:-
Step 1 — Launch an Ubuntu(22.04) T2 Large Instance
Step 2 — Install Jenkins, Docker and Trivy. Create a Sonarqube Container using Docker.
Step 3 — Install Plugins like JDK, Sonarqube Scanner, Nodejs, and OWASP Dependency Check.
Step 4 — Create a Pipeline Project in Jenkins using a Declarative Pipeline
Step 5 — Install OWASP Dependency Check Plugins
Step 6 — Docker Image Build and Push
Step 7 — Deploy the image using Docker
Step 8 — Terminate the AWS EC2 Instances.

# Note : You can use the  Elastic Ip if needed !!

# Let’s Start …..

# Step -1 :    Launch an Ubuntu(22.04) T2 Large Instance.

Instance  configurations:   
Family : t2
Type :  large
Ami : ubuntu 22.04 LTS
Storage : 30gb ( configure 30gb root volume to instance when your launching it)
Securtiy Group : Enable the HTTP & HTTPS

![1](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/93d6a484-ff6d-4f89-b968-322845fe4a75)

# Step 2 — Install Jenkins, Docker and Trivy. Create a Sonarqube Container using Docker.

sudo vi  Jenkins.sh
#!/bin/bash
sudo apt update -y

sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null

sudo apt-get update -y
sudo apt-get install openjdk-17-jre -y
sudo apt-get install jenkins -y
sudo systemctl start jenkins
sudo systemctl status Jenkins

# Note : Above steps you  can reffered through official Jenkins website (https://pkg.jenkins.io/debian-stable/)
---------------------------------------------------------------------------------------------------------------
# Give the Execution permission and the File :

sudo chmod +x <filename.sh>   or   sudo chmod 777 <filename.sh>

Run :  ./Jenkins.sh  or bash Jenkins.sh

# Access through  <public ip :8080 >
![2](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/2cb9f462-fc1c-48f8-9178-17411e365e5b)

# Go to terminal get the password 

sudo cat /var/lib/jenkins/secrets/initialAdminPassword

# 1.Jenkins Dashboard 

![3](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/5570597f-ca55-4ad4-844a-b6466470ba09)

# 2. Install Docker 

sudo apt-get install docker.io –y
sudo usermod -aG docker $USER
newgrp docker 
sudo chmod 777 /var/run/docker.sock

![4](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/7afeaf5f-985b-4f73-a6b0-8ef360520205)

# 3. Sonarqube installation through docker 

docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

![5](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/2a1a2b70-4624-42d1-bb7a-a8afc4b90596)

You can access the sonarqube  <public-ip : 9000>

By default  sonrqube creds:

# Username : admin
# Pass word : admin

# Sonarqube Dashboard :
![6](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/7dab4b3f-a2ca-4af9-a4ee-be8df8c6c0de)

# 4. Install Trivy :

sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy –y

![7](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/ab1d7d73-af01-4c19-a46f-0e5b9bcd272d)


# Step 3 — We will Install  Jenkins Plugins like JDK, Sonarqube Scanner, NodeJs, OWASP Dependency Check
  
  Jenkins Dashboard > Manage Jenkins > Plugins > Available Plugins 

1. Eclipse Temurin
     
![8](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/bf2b3625-9d30-468f-9ae3-bafae3781036)

2.Sonarqube scanner and Node js
![9](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/6a291d37-be2b-40a3-be8e-6679c7e2b8b1)

 Configure Java and Nodejs in Global Tool Configuration
Goto Manage Jenkins → Tools → Install JDK(17) and NodeJs(16)→ Click on Apply and Save
![10](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/fd048d3c-ca14-4f8d-abae-dd60032e37d5)

![11](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/59f34676-7fea-4184-84fc-753d0dc61739)

# Note : Create  a pipeline Job and give the name as Zomato 

# Step 4 — Configure Sonar Server in Manage Jenkins

Grab the Public IP Address of your EC2 Instance, Sonarqube works on Port 9000, so <Public IP>:9000. Goto your Sonarqube Server. Click on Administration → Security → Users → Click on Tokens and Update Token → Give it a name → and click on Generate Token

![12](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/4c75e7fe-bec7-4655-8ea1-0327511c6f13)

![13](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/d09c5d4a-94ba-4cfe-9f7d-da9d3dec3c20)

![14](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/4c92ba99-3b90-4a6d-8e46-6a47e9d680b4)

# copy Token and Goto Jenkins Dashboard → Manage Jenkins → Credentials → Add Secret Text. It should look like this

![15](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/2e760239-bed4-4c4e-a9e2-1bd0fb7bd238)

# Now, go to Dashboard → Manage Jenkins → System and Add like the below image.

![16](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/a974af83-dc6e-4eff-a253-f118e7a59ffb)

Click on Apply and Save . The Configure System option is used in Jenkins to configure different server . Global Tool Configuration is used to configure different tools that we install using Plugins
We will install a sonar scanner in the tools

![17](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/459b018d-c120-407e-bab1-4f6d975f5e88)

# Enabling the Sonarqube 

In the Sonarqube Dashboard add a quality gate also Administration → Configuration →Webhooks

![18](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/7e3ec23a-ced2-42c7-a061-0e2a18ecc735)


Note : Pipeline scrip & Dockerfile is provided in repo 
#  if pipeline is not working !! check the syntactic and dependency correct names, versions as well


# Step 5 — Install OWASP Dependency Check Plugins
GotoDashboard → Manage Jenkins → Plugins → OWASP Dependency-Check. Click on it and install it without restart.

![21](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/174637f6-a55d-4759-b8bb-24ed9f9ad475)

First, we configured the Plugin and next, we had to configure the Tool
Goto Dashboard → Manage Jenkins → Tools →

![22](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/b79472ac-9091-459b-8d66-bb545768ed9a)

# Step 6 — Docker Image Build and Push
We need to install the Docker tool in our system, Goto Dashboard → Manage Plugins → Available plugins → Search for Docker and install these plugins

Docker, docker pipeline, docker build-step, docker commons

![24](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/2c394952-3e2d-48e6-9767-ceefc4c3f25e)

![25](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/f318e122-fc82-4d16-a59d-fc637ecaf59f)

![26](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/fb11faed-9ec5-4a59-81b8-6d9b281a3dbd)



# Before running the script : Make sure that Jenkins user is added to docker group 

sudo usermod -aG docker Jenkins


Pipleine Build Successfull and Learn to debug if any error occurs

![27](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/e237ea24-aad2-423a-846b-830f1e0c0f2f)


Final Result shoud like this 

![28](https://github.com/mohsuhel/zomato-cloned--ci-cd-steps/assets/127845338/f8e086f3-2d3d-4fc5-a1b0-dcf70837e7b6)
































