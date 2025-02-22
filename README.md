# Registration App CI/CD Pipeline

## Overview

This project is a forked version of a registration application that includes a CI/CD pipeline using Jenkins. The pipeline builds a Maven-based Java web application (`.war` file), creates a Docker image, and pushes it to Docker Hub. The setup is implemented on an AWS EC2 instance.

## Prerequisites

Before setting up the CI/CD pipeline, ensure the following requirements are met:

- **AWS EC2 Instance** (Amazon Linux 2 / Ubuntu)
- **Jenkins Installed and Configured**
- **GitHub Repository Forked** and Webhook Configured
- **Docker Installed on EC2**
- **Maven Installed on EC2**
- **Docker Hub Account** with Repository Created

## Project Structure

```
registration-app/
│── src/
│── pom.xml
│── Dockerfile
│── Jenkinsfile
│── README.md
```

## CI/CD Pipeline Workflow

1. **Code Push:** Developer pushes changes to GitHub.
2. **Jenkins Trigger:** GitHub Webhook triggers Jenkins job.
3. **Build Application:** Jenkins compiles the code and builds a `.war` file using Maven.
4. **Create Docker Image:** Jenkins builds a Docker image using `Dockerfile`.
5. **Push to Docker Hub:** The Docker image is pushed to Docker Hub.
6. **Deploy (Optional):** The container can be deployed using Docker or Kubernetes.

## Jenkinsfile

The `Jenkinsfile` automates the build and deployment process:

```groovy
pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'aswin21030'  
        IMAGE_NAME = 'aswin21030/jenkins-myapp'  
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/ASWIN-2103/registration-app.git'
            }
        }

        stage('Build WAR File') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
               withCredentials([string(credentialsId: 'docker-hub-token', variable: 'DOCKER_ACCESS_TOKEN')]) {
                  sh 'echo "$DOCKER_ACCESS_TOKEN" | docker login -u "$DOCKER_HUB_USER" --password-stdin'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }
    }
}
```

## Setting Up Jenkins on EC2

### Install Jenkins

```bash
sudo yum update -y
sudo yum install java-11-amazon-corretto -y
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
sudo yum install jenkins -y
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

### Install Docker & Configure Jenkins Permissions

```bash
sudo yum install docker -y
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker jenkins
```

Restart Jenkins after adding it to the `docker` group.

### Install Maven

```bash
sudo yum install maven -y
```

### Configure Docker Hub Credentials in Jenkins

1. Go to **Jenkins Dashboard** > **Manage Jenkins** > **Manage Credentials**.
2. Add a new credential:
   - **Kind:** Username and password
   - **Username:** Your Docker Hub username
   - **Password:** Your Docker Hub password
   - **ID:** `docker-hub-credentials`

## Running the Application

Once the Jenkins pipeline runs successfully and pushes the Docker image, you can run the application:

```bash
docker run -d -p 9090:8080 your-dockerhub-username/registration-app
```

Access the application at: `http://<EC2-PUBLIC-IP>:8080`

## Future Enhancements

- Implement Kubernetes Deployment
- Add Automated Tests in Jenkinsfile
- Set Up AWS ECS or EKS for Deployment

## Conclusion

This project demonstrates an automated CI/CD pipeline for a Java web application using Jenkins, Docker, and AWS EC2. Feel free to contribute and enhance the workflow!


