pipeline {
    agent any

    environment {
        APP_NAME = "nodejs-app"
        IMAGE_NAME = "saga12saho/nodejs-app"
        NAMESPACE = "default"
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

        stage('Docker Login') {
    steps {
        withCredentials([usernamePassword(credentialsId: 'docker-cred',
                                          usernameVariable: 'DOCKER_USER',
                                          passwordVariable: 'DOCKER_PASS')]) {

            sh '''
                echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
            '''
        }
    }
}

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$BUILD_NUMBER .'
            }
        }

         stage('Push Docker Image') {
            steps {
                sh 'docker push $IMAGE_NAME'
            }
        }

                stage('Deploy to Kubernetes') {
    steps {

        sh 'kubectl version --client'

        sh 'kubectl apply -f K8s/deployment.yaml'

        sh 'kubectl apply -f K8s/service.yaml'

        
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
