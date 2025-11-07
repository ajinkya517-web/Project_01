pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')   
        FRONTEND_IMAGE = "abhi2305/frontend-app:latest"
        BACKEND_IMAGE = "abhi2305/backend-app:latest"
        REACT_APP_SERVER_URL = "http://54.204.175.86:30002"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/ajinkya517-web/Project_01.git'
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                sh """
                cd backend
                docker build -t ${BACKEND_IMAGE} .
                """
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                sh """
                cd frontend
                npm install --legacy-peer-deps
                REACT_APP_SERVER_URL=${REACT_APP_SERVER_URL} npm run build
                docker build -t ${FRONTEND_IMAGE} .
                """
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                sh """
                echo "${DOCKERHUB_CREDENTIALS_PSW}" | docker login -u "${DOCKERHUB_CREDENTIALS_USR}" --password-stdin
                docker push ${BACKEND_IMAGE}
                docker push ${FRONTEND_IMAGE}
                """
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl apply -f k8s/
                kubectl rollout restart deployment/frontend
                kubectl rollout restart deployment/backend-deployment
                """
            }
        }
    }
}
