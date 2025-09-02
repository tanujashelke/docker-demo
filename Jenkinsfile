pipeline {
    agent any

    environment {
        IMAGE_NAME = 'demo-app'
        IMAGE_TAG = "${env.BUILD_NUMBER}" 
        KUBE_MANIFEST = "demo-app.yaml"
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10')) 
        timestamps()
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Cloning Git repository..."
                git branch: 'main', url: 'https://github.com/tanujashelke/docker-demo.git'
            }
        }

        stage('Build Maven Project') {
            steps {
                echo "Building Spring Boot project..."
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image ${IMAGE_NAME}:${IMAGE_TAG}..."
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    try {
                        echo "Deploying to Kubernetes..."
                        
                       
                        sh "sed -i 's|image: ${IMAGE_NAME}:latest|image: ${IMAGE_NAME}:${IMAGE_TAG}|g' ${KUBE_MANIFEST}"
                        
                
                        sh "kubectl apply -f ${KUBE_MANIFEST}"
                        
                        
                        sh "kubectl rollout status deployment/${IMAGE_NAME}-deployment --timeout=120s"
                    } catch (err) {
                        echo "Deployment failed! Rolling back..."
                        sh "kubectl rollout undo deployment/${IMAGE_NAME}-deployment"
                        error "Deployment failed, rollback executed."
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Build and deployment succeeded! Image: ${IMAGE_NAME}:${IMAGE_TAG}"
        }
        failure {
            echo "Build or deployment failed."
        }
        always {
            echo "Cleaning up local Docker images..."
            sh "docker image prune -f"
        }
    }
}
