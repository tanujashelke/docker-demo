pipeline {
    agent any

    environment {
        IMAGE_NAME = 'demo-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/tanujashelke/docker-demo.git'
            }
        }

        stage('Build Maven Project') {
            steps {
                bat 'mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t demo-app:latest .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl apply -f demo-app.yaml'
            }
        }
    }

    post {
        always {
            bat 'docker images'
        }
    }
}
