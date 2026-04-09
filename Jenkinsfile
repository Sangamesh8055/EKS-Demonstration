pipeline {
    agent any

    environment {
        AWS_REGION = "ap-south-1"
        ECR_REPO = "828360850570.dkr.ecr.ap-south-1.amazonaws.com/my-app"
        CLUSTER_NAME = "dev-eks"
    }

    stages {

        stage('Clone Code') {
            steps {
                git 'https://github.com/Sangamesh8055/EKS-Demonstration.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t my-app .'
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag my-app:latest $ECR_REPO:latest'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                aws ecr get-login-password --region $AWS_REGION \
                | docker login --username AWS --password-stdin $ECR_REPO
                '''
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $ECR_REPO:latest'
            }
        }

        stage('Deploy to EKS') {
            steps {
                sh '''
                aws eks update-kubeconfig \
                  --region $AWS_REGION \
                  --name $CLUSTER_NAME

                kubectl apply -f k8s.yaml
                '''
            }
        }
    }
}
