pipeline {
    agent any

    environment {
        AWS_REGION   = 'ap-south-1'
        ECR_REGISTRY = '340695329634.dkr.ecr.ap-south-1.amazonaws.com'
        IMAGE_NAME   = 'nodejs-app'
        IMAGE_TAG    = "${env.BUILD_NUMBER}"

        // AWS credentials from Jenkins
        AWS_ACCESS_KEY_ID     = credentials('aws-creds')
        AWS_SECRET_ACCESS_KEY = credentials('aws-creds')
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'dev', credentialsId: 'github-https-creds', url: 'https://github.com/Vidumukhijadhav/DevOps-Task-I.git'
            }
        }

        stage('Build & Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
            }
        }

        stage('ECR Login & Push') {
            steps {
                sh '''
                    # Configure AWS CLI
                    aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                    aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                    aws configure set region $AWS_REGION

                    # Login to ECR (registry only)
                    aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REGISTRY

                    # Tag and push Docker image
                    docker tag $IMAGE_NAME:$IMAGE_TAG $ECR_REGISTRY/$IMAGE_NAME:$IMAGE_TAG
                    docker push $ECR_REGISTRY/$IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }
}
