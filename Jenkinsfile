pipeline {
    agent any

    environment {
        APP_NAME = "nodejs-app"
        IMAGE_NAME = "nodejs-app"
        NAMESPACE = "default"

    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                    docker build -t nodejs-app:latest .
                    minikube image load nodejs-app:latest              
                '''
            }
        }

      stage('Verify Deployment') {
            steps {
                sh '''
                echo "Checking pods..."

                kubectl get pods -n ${NAMESPACE}

                echo "Checking services..."

                kubectl get svc -n ${NAMESPACE}

                echo "Deployment verified successfully"
                '''
            }
        }

    }
}
