pipeline {
    agent any

    environment {
        IMAGE_NAME = "heroku-node-app:latest"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/abisecops/nodejs-getting-started'
            }
        }

        stage('Build Docker Image name from environment') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Load Docker Image to Minikube') {
            steps {
                sh 'minikube image load $IMAGE_NAME'
            }
        }

        stage('Generate Deployment YAML from imperative command') {
            steps {
                sh '''
                kubectl create deployment heroku-node-app \
                  --image=heroku-node-app:latest \
                  --dry-run=client -o yaml > deployment.yaml
                '''
            }
        }

        stage('Generate Service YAML file from imperative') {
            steps {
                sh '''
                kubectl expose deployment heroku-node-app \
                  --type=NodePort --port=5000 --target-port=5000 \
                  --name=heroku-node-service \
                  --dry-run=client -o yaml > service.yaml
                '''
            }
        }

        stage('Deploy to Minikube') {
            steps {
                sh '''
                kubectl apply -f deployment.yaml
                kubectl apply -f service.yaml
                '''
            }
        }

        stage('Access the service from url') {
            steps {
                sh 'minikube service heroku-node-service --url'
            }
        }
    }
}
