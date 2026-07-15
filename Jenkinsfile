pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        ECR_REPO = '676327216025.dkr.ecr.us-east-1.amazonaws.com/test-docker-project'
        IMAGE_NAME = 'test-docker-project:django'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/jbrooks0929/test-docker-project.git'
                    ]]
                )
            }
        }

        stage('Login to ECR') {
            steps {
                withAWS(region: "${AWS_REGION}", credentials: 'aws-creds') {
                    powershell '''
                    $ErrorActionPreference = "Stop"

                    aws --version

                    aws ecr get-login-password --region $env:AWS_REGION |
                        docker login --username AWS --password-stdin $env:ECR_REPO
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                powershell '''
                $ErrorActionPreference = "Stop"

                docker build -t $env:IMAGE_NAME .

                docker tag $env:IMAGE_NAME $env:ECR_REPO:latest
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                powershell '''
                $ErrorActionPreference = "Stop"

                docker push $env:ECR_REPO:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Docker image successfully pushed to Amazon ECR.'
        }

        failure {
            echo 'Pipeline failed.'
        }
    }
}
