pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        IMAGE_NAME = "maheshbommu/jenkins-demo-app"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:maheshbommu/Web-App.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $IMAGE_NAME:latest .'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
                    sh 'docker push $IMAGE_NAME:latest'
                }
            }
        }

        stage('Deploy Container') {
            steps {
                script {
                    // Stop and remove any existing container
                    sh 'docker ps -q --filter "name=jenkins-demo-app" | grep -q . && docker stop jenkins-demo-app && docker rm jenkins-demo-app || true'
                    // Run new container
                    sh 'docker run -d -p 5000:5000 --name jenkins-demo-app $IMAGE_NAME:latest'
                }
            }
        }
    }
}

