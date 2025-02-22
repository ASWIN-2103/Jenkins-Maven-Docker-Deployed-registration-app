pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'aswin21030'  
        DOCKER_HUB_PASS = credentials('2546eaff-3c64-4c77-91ad-07b381e67189') 
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
                sh 'echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin'
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }
    }
}
