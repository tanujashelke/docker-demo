pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/tanujashelke/docker-demo.git'
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t demo-app:latest .'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f demo-app.yaml'
            }
        }
    }

    post {
        always {
            sh 'docker images' 
        }
    }
}
