pipeline {
    agent any

    environment {
        APP_NAME = "nodejs-app"
        IMAGE_NAME = "nodejs-app"
        NAMESPACE = "jenkins"
        KUBECONFIG = "/var/lib/jenkins/.kube/config"

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
                echo "Deploying application to Minikube..."

                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml

                echo "Waiting for deployment rollout..."

                kubectl rollout status deployment/${APP_NAME} \
                --namespace=${NAMESPACE} \
                --timeout=180s
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
