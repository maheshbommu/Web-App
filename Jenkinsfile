pipeline {
    agent any

    environment {
        IMAGE_NAME = "maheshbommu/jenkins-demo-app"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "🔹 Cloning repository..."
                git branch: 'main',
                    credentialsId: 'github', // your GitHub credentials ID
                    url: 'https://github.com/maheshbommu/Web-App.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "🔹 Building Docker image..."
                script {
                    // Get short git commit hash
                    GIT_COMMIT = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    sh "docker build -t ${IMAGE_NAME}:latest -t ${IMAGE_NAME}:${GIT_COMMIT} ."
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                echo "🔹 Logging in to Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKERHUB_USR', passwordVariable: 'DOCKERHUB_PASS')]) {
                    sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USR --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "🔹 Pushing Docker image to Docker Hub..."
                script {
                    // Push both latest and commit hash tags
                    sh "docker push ${IMAGE_NAME}:latest"
                    sh "docker push ${IMAGE_NAME}:${GIT_COMMIT}"
                }
            }
        }

        stage('Deploy Container') {
            steps {
                echo "🔹 Deploying container..."
                // Example deployment: stop & run container (adjust to your needs)
                sh """
                docker rm -f jenkins-demo-app || true
                docker run -d --name jenkins-demo-app -p 5000:5000 ${IMAGE_NAME}:latest
                """
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully."
        }
        failure {
            echo "❌ Pipeline failed. Check logs for errors."
        }
    }
}

