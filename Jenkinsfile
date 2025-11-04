pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = "frontend-app"
        BACKEND_IMAGE  = "backend-app"
        ENV_FILE       = "./backend/.env"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/ajinkya517-web/Project_01.git'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                echo "Building Docker images..."
                docker build -t $FRONTEND_IMAGE ./frontend
                docker build -t $BACKEND_IMAGE ./backend
                '''
            }
        }

        stage('Load Images into Minikube') {
            steps {
                sh '''
                echo "Loading images into Minikube..."
                minikube image load $FRONTEND_IMAGE
                minikube image load $BACKEND_IMAGE
                '''
            }
        }

        stage('Deploy Backend on Kubernetes') {
            steps {
                sh '''
                echo "Deploying backend..."
                # Clean up old deployment if it exists
                kubectl delete deployment backend --ignore-not-found
                kubectl delete service backend-service --ignore-not-found

                # Apply YAML for backend
                kubectl apply -f k8s/backend.yaml

                # Create Kubernetes Secret from backend .env file
                kubectl delete secret backend-env --ignore-not-found
                kubectl create secret generic backend-env --from-env-file=$ENV_FILE

                # Attach the secret to the running backend deployment
                kubectl set env deployment/backend --from=secret/backend-env
                '''
            }
        }

        stage('Deploy Frontend on Kubernetes') {
            steps {
                sh '''
                echo "Deploying frontend..."
                kubectl delete deployment frontend --ignore-not-found
                kubectl delete service frontend-service --ignore-not-found
                kubectl apply -f k8s/frontend.yaml
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                echo "Verifying pods and services..."
                kubectl get pods -o wide
                kubectl get svc
                '''
            }
        }
    }
}
