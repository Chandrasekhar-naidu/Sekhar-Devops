# Sekhar-Devops
website link: file:///C:/Users/user/Desktop/HTML%20@%20CSS/Sekhar.html/index.html



Understanding the Continuous Integration Process
Building a CI/CD pipeline might sound complicated, but it’s one of the most important skills you can have as a DevOps engineer! A CI/CD pipeline is all about automating the process of building, testing, and deploying applications so your code goes from your laptop to production smoothly and securely. One key step is to create a Docker image, which is like packing your Flask app with everything it needs to run into a neat, portable box. In an enterprise setting, this is non-negotiable. It ensures your app works the same way, whether it’s running on a developer’s machine, a staging server, or in production. Also, a secure pipeline helps catch issues early, enforces quality, and keeps everything compliant with company standards. Understanding how all the pieces work together like code repositories, testing, image security, and deployments gives you the power to ship better software faster and with confidence. I will show you step by step on how it is done and give more insight into my process on doing this!

Introduction to Docker and Containerization
As we delve into the world of Docker and containerization, our focus is on simplifying deployment processes and ensuring applications can run consistently across various environments. This guide captures a session in which we developed a Node.js web application, packaged it into a Docker container, pushed it to Docker Hub, and set up an automated CI/CD pipeline. Whether you’re new to Docker or looking to reinforce your skills, this tutorial provides the foundational knowledge to containerize a web app and understand the development lifecycle.

Understanding Docker Basics

Before diving into our session, it’s essential to understand a few fundamental concepts about Docker:

Docker Containers — Containers are lightweight, executable units of software in which application code, dependencies, and runtime environments are packaged. They provide consistency and portability across different environments.
Docker Images — An image is a snapshot of a container’s file system and is used to create instances of containers.
Docker Hub — Docker Hub is a cloud-based repository where Docker images are stored and shared.
Session Walkthrough

During our session, we covered the steps necessary to build a Docker image, test it locally, and push it to Docker Hub for sharing and deployment. We also introduced the concept of a CI/CD pipeline using Jenkins to automate these steps in production environments. We will be using the Dockerfile code below for our project.Here’s a step-by-step guide to recreating the process:

FROM node:18-alpine
WORKDIR /app
COPY ./node-webapp .
RUN yarn install --production
CMD ["node" , "src/index.js"]
EXPOSE 3000

1. Setting Up the Docker Environment
Current Directory Reference (.): In Docker, a dot (.) is used to indicate the current directory. When specifying .in a Docker command, it instructs Docker to look in the current directory for files like Dockerfile, which defines the instructions for building the Docker image.

2.Dockerfile Basics: The Dockerfile is a text document that contains all the commands used to assemble an image. We used a Dockerfile to set up the Node.js environment, copy our web application code, install dependencies, and expose the necessary ports for our application to be accessible.

3. Building and Testing the Docker Image
   
Build the Image: Use the command docker build -t <image-name> . to build the Docker image. The -t flag assigns a name to the image for easy reference.

Run the Container: Once built, the command docker run -d -p 3000:3000 <image-name> runs the container in detached mode (-d), mapping port 3000 on the container to port 3000 on the host.

Verify the Container: We verified that the application was running by opening localhost:3000 in a browser. If the app loads correctly, our Docker container has successfully hosted the application.

5. Resolving Common Errors
Node.js Image Version Issue: We encountered an error while using a specific version of the Node.js image. The solution was to manually pull the correct version using docker pull node:<version>-alpine.

6.Manual Pull vs. Automatic Image Download: Ideally, Docker automatically pulls images from Docker Hub, but manual pulling can be necessary when certain configurations aren’t detected automatically.

7. Docker Hub: Pushing the Image
Tagging the Image: To push our Docker image to Docker Hub, it needs to be tagged with the correct repository name using the command docker tag <local-image-name> <docker-hub-username>/<repository-name>:v1.

8. Push to Docker Hub: The command docker push <docker-hub-username>/<repository-name>:v1 pushes the tagged image to Docker Hub. Once the image is on Docker Hub, it’s available for others to pull and deploy.
   
9. Repository Management: For larger projects, repository management tools like Nexus or Amazon’s ECR (Elastic Container Registry) are commonly used in place of Docker Hub.
10. Automating with Jenkins: CI/CD Pipeline
CI/CD Process Overview: A CI/CD pipeline automates the build, test, and deployment processes. We will be using Jenkins a CI/CD tool that helps automate these tasks to streamline the software development lifecycle (SDLC).


Pipeline Setup: In the next phase of our project, we will set up a pipeline where Jenkins automatically pulls code from GitHub, builds the Docker image, tags it and pushes it to Docker Hub.


Automated Deployment: With automation in place, we eliminate repetitive tasks allowing for quicker releases and more consistent deployments.
— — — — — — — — — — — — — — — — — — — -


Launch EC2 Instances:
First thing lets create an EC2 instance and connect it to get started. Go to your AWS Console and create two new EC2 instances.

Operating System: Select Ubuntu as the OS.
Instance Type: Choose T2.medium for both instances to have adequate resources.
Storage: Configure each instance with 20GB storage.
Instance Naming: Name one instance “Jenkins-Master” and the other “Jenkins-Slave.”
Create Key Pair:
While creating the instances, generate a new Key Pair to allow SSH access. For example keypair: Jenkins. Then Download the Key (keep it secure; we’ll need it later for SSH connections)



How I setup my security inbound rules for Jenkins
After that is done, configure the correct security group.

Allow the necessary ports for Jenkins and SSH:

Port 8080 for Jenkins web access.
Port 22 for SSH.
Port 50000 (optional, required if Slave architecture needs it).
Once this is done, we launch the instances. These steps should give you a fully initialized pair of instances, ready for configuration.

2. Installing Docker on Jenkins Master
Set up Docker on the Master instance to run Jenkins without manual installation.

SSH into the Master Instance:
Navigate to the EC2 dashboard. Select your Jenkins-Master instance, click on Connect, and choose EC2 Instance Connect to establish the SSH session.

Install Docker: Run the following commands:
 curl -fsSL https://get.docker.com -o get-docker.sh
 sudo sh get-docker.sh
 sudo groupadd docker
 sudo usermod -aG docker $USER

please review the docker site regarding installation of ubuntu
sudo docker run hello-world

Now your docker has been installed
The Docker is successfully installed! the next step is to configure it for streamlined command execution. By default, Docker commands require sudo privileges, which can add extra steps when running frequent tasks. To simplify this, we add our user to the Docker user group allowing Docker commands to be executed without needing sudo each time. This approach enhances productivity and is especially useful in a CI/CD environment where frequent commands are automated.

Here’s how it works:

Adding User to Docker Group
By assigning our user to the Docker group, we give it permission to run Docker commands without elevated privileges. This eliminates the need to type sudo before every command, allowing for smoother operation in both manual and automated scenarios.
Restarting the Instance
For this change to take effect, we’ll restart the instance. After rebooting, Docker commands should work seamlessly without sudo.

Verification
Once the instance is back up, verify the setup by running:
docker run hello-world
After confirming this setup on the Master instance, repeat these post-installation steps for the slave node. With Docker now configured both instances, we’re ready to proceed with the Jenkins setup!

Installing Jenkins on Master server
Installing a Dockerized Jenkins command to our Master instance instead of the slave EC2 aligns with the best practices of separating responsibilities in a Jenkins master-slave architecture.

The Master Node Server Responsibilities:

The master node in Jenkins is responsible for orchestrating the CI/CD pipeline, managing configurations, and maintaining the state of the entire Jenkins system. It acts as the brain of the operation, handling tasks such as:

Configuring and triggering builds.
Monitoring pipeline execution and agent health.
Managing credentials and plugins.
Running Jenkins in Docker on the master node ensures that the core Jenkins instance is encapsulated, portable, and easier to maintain, upgrade, or migrate if necessary.

As for the Slave Node Server Responsibilities:

The slave (or agent) nodes are designed to execute the actual build, test, and deployment tasks assigned by the master. They are optimized for:

Running resource-intensive jobs without impacting the master’s performance.
Scaling horizontally by adding more agents to distribute workloads.
By keeping the slave node lightweight and focused solely on execution, you avoid overloading it with additional management overhead like hosting Jenkins itself. This design ensures a scalable, efficient and maintainable CI/CD pipeline that leverages Docker for streamlined infrastructure management.

Use the command below to install your Jenkins.

sudo docker run -d -p 8080:8080 -p 50000:50000 -v /home/ubuntu:/var/jenkins_home jenkins/jenkins


Enter the initial password hash you retrived from the terminal

After the download is complete, open your browser and navigate to http://<Master_Public_IP>:8080. Then input the administrator password there

Jenkins will prompt you to install plugins. Select Install Suggested Plugins. Then create an Admin User with a username and password that you will remember. Once these plugins are installed, Jenkins on your Master is ready to go!

Configuring Jenkins Master-Slave Connection
Now we are going to connect the Master and Slave in Jenkins so tasks can be assigned to the Slave for execution. Creating a node in Jenkins is essential to establish the environment where the pipeline tasks will be executed. Nodes referred to as “agents” or “slave” are configured machines that perform specific tasks in the Jenkins ecosystem. They are the backbone of a distributed Jenkins setup.

Here is why nodes are necessary:

Distributed Builds: Nodes allow Jenkins to offload resource-intensive tasks such as compiling code, running tests, or building Docker images, preventing the master from becoming overloaded. This ensures efficient resource utilization across multiple machines.
2. Task Isolation: Different nodes can be configured with distinct environments (e.g., Java, Python, or Docker) to match the requirements of specific projects or pipelines. This ensures builds run in consistent, isolated settings.

3. Scalability: Adding nodes helps scale Jenkins horizontally, enabling it to handle a growing number of jobs and users without performance degradation.

4. Role Separation: Nodes maintain the separation of roles in a master-slave architecture. The master focuses on pipeline orchestration and configuration, while nodes handle execution, providing better fault tolerance.

5. Parallel Execution: With multiple nodes, Jenkins can run jobs concurrently, speeding up the CI/CD process, which is critical for large projects with frequent updates.

To Create a Jenkins Node
Go to Jenkins Dashboard:

On the Master’s Jenkins dashboard go to Manage Jenkins → Manage Nodes & Clouds → New Node.


2. Create the Slave Node:

Node Name: Docker-Slave
Type: Permanent Agent
Number of Executors: 2 (since it’s a T2.medium)
Remote Directory: /home/ubuntu/jenkins
Label: Docker
Launch Method: SSH

3. Add SSH Credentials:

In Jenkins, go to the credentials field, select Add, then choose SSH Username with Private Key.
Use the following:
ID: SSH (or any other identifier)
Username: ubuntu
Private Key: Copy and paste the contents of your .pem key (Jenkins Key Pair) here.


Click save and Launch the agent. Ensure the Host Key Verification Strategy is Non verifying.

Ensure the Kind: SSH Username with private key & Scope is Global

Commiting and Pushing Changes to GitHub
Now we setup on Jenkins to push our dockerized application through a robust CI/CD pipeline. First thing is to Commit and Push changes to GitHub.


Copy the HTTPS link from your repository that you’re about to use.

Go to new item and click on pipeline

On general set up your log rotations in this format and paste the github project link

Paste it in the Pipeline configurations Repo URL at “Advanced Project Options”

Change branch to main and save the changes at “Pipeline”.
Next you’re going to click build now and open your IDE. We’re adding the Jenkins file to pipeline.


Use this code to create your pipeline,

pipeline {
    agent { label 'slave'}

    environment {
        DOCKER_HUB_REPO = 'farooqdevops/my-node-app' // Replace with your Docker Hub repo
        DOCKER_HUB_CREDENTIALS_ID = 'docker-hub' // Jenkins credentials ID for Docker Hub
        IMAGE_TAG = "latest" // This can be dynamic, like using a commit hash or build number
        NEW_IMAGE_TAG = "v3.0.0" // Replace with the tag version you want to use
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    dir('docker') {
                    sh 'ls -l'
                    // Build Docker image using Dockerfile in the current directory
                    sh "docker build -t ${DOCKER_HUB_REPO}:${IMAGE_TAG} ."
                    }
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                script {
                    // Tag the image with a new version or tag
                    sh "docker tag ${DOCKER_HUB_REPO}:${IMAGE_TAG} ${DOCKER_HUB_REPO}:${NEW_IMAGE_TAG}"
                }
            }
        }

        stage('Login and Push to Docker Hub') {
            steps {
                script {
                    // Login to Docker Hub and push both the latest and the new tag
                    withCredentials([usernamePassword(credentialsId: DOCKER_HUB_CREDENTIALS_ID, usernameVariable: 'DOCKER_HUB_USERNAME', passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        // Login to Docker Hub
                        sh 'docker login -u $DOCKER_HUB_USERNAME -p $DOCKER_HUB_PASSWORD'

                        // Push both tags (latest and new version)
                        sh "docker push ${DOCKER_HUB_REPO}:${NEW_IMAGE_TAG}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker images from the Jenkins agent to save space
            sh "docker rmi ${DOCKER_HUB_REPO}:${IMAGE_TAG} || true"
            sh "docker rmi ${DOCKER_HUB_REPO}:${NEW_IMAGE_TAG} || true"
        }
    }
}

Go to Jenkins file terminal and proceed to first step 1. git add Jenkinsfile 2. git status

Step 3. git commit -m “name folder” Step 4. git push

git push — set-upstream origin <project name>

5.Git checkout main 6. git fetch 7.git pull
Navigate to your GitHub repository to create a Pull Request and merge your changes.


Click Compare and Pull Request. Add relevant comments and select Merge Pull Request.
Updating Docker Hub Repository
Log in to Docker Hub via your web browser and locate your repository. You’re going to update old repository paths with the updated repository name in your Dockerfile.


Go to Dockerfile Terminal with these steps: 1. git add Jenkinsfile 2. git commit -m <docker repo name> 3. git push

Configuring Jenkins for CI/CD
In Jenkins, navigate to Manage Jenkins → Credentials → System → Global Credentials. Click Add Credentials and provide:

Username: Docker Hub username/email.
Password: Docker Hub password.
ID: docker-hub.

Configure the Jenkins pipeline
In the Jenkins dashboard, navigate to your pipeline project for example “Docker CI/CD”.


Trigger build by clicking Build Now
Pipeline Execution Process

Once the build starts, Jenkins will execute the following stages in the CI/CD pipeline:

Code Checkout
Fetches the latest code from the GitHub repository.
2. Docker Build

Switches to the Docker directory and builds the Docker image using the Dockerfile.
3. Docker Tag and Push

Tags the image with a version number (e.g., v3.0.0) and pushes it to Docker Hub.
4. Clean-Up

Deletes temporary Docker images from the Jenkins agent to optimize resource usage.
To verify, navigate to your Docker Hub repository. You should see the new Docker image (e.g., version v3.0.0) successfully uploaded.



Here’s our completed CICD Pipeline build
