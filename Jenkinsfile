pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/ajinkya517-web/Project_01.git'
        FRONTEND_IMAGE = 'frontend-app:latest'
        BACKEND_IMAGE = 'backend-app:latest'
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo "Cloning repository..."
                git branch: 'main', url: "${REPO_URL}"
            }
        }

        stage('Build Docker Images') {
            steps {
                echo "Building Docker images for frontend and backend..."
                sh '''
                cd frontend
                docker build -t ${FRONTEND_IMAGE} .
                cd ../backend
                docker build -t ${BACKEND_IMAGE} .
                docker images
                '''
            }
        }

        stage('Create Kubernetes Manifests') {
            steps {
                echo "Creating simple Kubernetes deployment and service files..."
                sh '''
                mkdir -p k8s

                cat <<EOF > k8s/frontend-deployment.yaml
                apiVersion: apps/v1
                kind: Deployment
                metadata:
                  name: frontend-deployment
                spec:
                  replicas: 1
                  selector:
                    matchLabels:
                      app: frontend
                  template:
                    metadata:
                      labels:
                        app: frontend
                    spec:
                      containers:
                      - name: frontend
                        image: ${FRONTEND_IMAGE}
                        ports:
                        - containerPort: 3000
                ---
                apiVersion: v1
                kind: Service
                metadata:
                  name: frontend-service
                spec:
                  selector:
                    app: frontend
                  type: NodePort
                  ports:
                  - port: 3000
                    targetPort: 3000
                    nodePort: 30080
                EOF

                cat <<EOF > k8s/backend-deployment.yaml
                apiVersion: apps/v1
                kind: Deployment
                metadata:
                  name: backend-deployment
                spec:
                  replicas: 1
                  selector:
                    matchLabels:
                      app: backend
                  template:
                    metadata:
                      labels:
                        app: backend
                    spec:
                      containers:
                      - name: backend
                        image: ${BACKEND_IMAGE}
                        ports:
                        - containerPort: 5000
                ---
                apiVersion: v1
                kind: Service
                metadata:
                  name: backend-service
                spec:
                  selector:
                    app: backend
                  type: NodePort
                  ports:
                  - port: 5000
                    targetPort: 5000
                    nodePort: 30081
                EOF
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                echo "Deploying application to Kubernetes..."
                sh '''
                kubectl apply -f k8s/
                kubectl get pods -o wide
                kubectl get svc
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Application deployed successfully!"
        }
        failure {
            echo "❌ Pipeline failed. Check logs for details."
        }
    }
}
