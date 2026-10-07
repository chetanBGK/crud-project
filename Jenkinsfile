pipeline {
    agent any

    environment {
        AWS_REGION = 'ap-south-1'
        AWS_ACCOUNT_ID = '819168518875'
        ECR_REPO_NAME_BACKEND = 'crud-backend'
        ECR_REPO_NAME_FRONTEND = 'crud-frontend'
        ECR_REGISTRY = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/chetanBGK/crud-project.git'
            }
        }
        stage('Build Backend') {
            steps {
                // Dockerfile and pom.xml are at the root level
                sh 'docker build -t crud-backend:${BUILD_NUMBER} .'
            }
        }

        stage('Build Frontend') {
            steps {
                dir('Crud-frontend') {
                    sh 'docker build -t crud-frontend:${BUILD_NUMBER} .'
                }
            }
        }

        stage('Push to ECR') {
            steps {
                script {
                    // Login to AWS ECR
                    sh "aws ecr get-login-password --region ${AWS_REGION} | docker login --username AWS --password-stdin ${ECR_REGISTRY}"

                    // Tag and push backend image
                    sh "docker tag crud-backend:${BUILD_NUMBER} ${ECR_REGISTRY}/${ECR_REPO_NAME_BACKEND}:${BUILD_NUMBER}"
                    sh "docker push ${ECR_REGISTRY}/${ECR_REPO_NAME_BACKEND}:${BUILD_NUMBER}"

                    // Tag and push frontend image
                    sh "docker tag crud-frontend:${BUILD_NUMBER} ${ECR_REGISTRY}/${ECR_REPO_NAME_FRONTEND}:${BUILD_NUMBER}"
                    sh "docker push ${ECR_REGISTRY}/${ECR_REPO_NAME_FRONTEND}:${BUILD_NUMBER}"
                }
            }
        }
        
    }
}