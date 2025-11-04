pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = "frontend-app"
        BACKEND_IMAGE  = "backend-app"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/ajinkya517-web/Project_01.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                docker build -t $FRONTEND_IMAGE ./frontend
                docker build -t $BACKEND_IMAGE ./backend
                '''
            }
        }

        stage('Load Images into Minikube') {
            steps {
                sh '''
                minikube image load $FRONTEND_IMAGE
                minikube image load $BACKEND_IMAGE
                '''
            }
        }

        stage('Deploy on Kubernetes') {
            steps {
                sh '''
                kubectl apply -f k8s/frontend.yaml
                kubectl apply -f k8s/backend.yaml
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                kubectl get pods
                kubectl get svc
                '''
            }
        }
    }
}
