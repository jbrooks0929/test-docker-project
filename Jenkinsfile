pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/jbrooks0929/test-docker-project'
            }
        }

        stage('Login to ECR') {
            steps {
                withAWS(region: 'us-east-1', credentials: 'aws-creds') {
                    powershell '''
                    aws ecr get-login-password --region us-east-1 |
                    docker login --username AWS --password-stdin 676327216025.dkr.ecr.us-east-1.amazonaws.com
                    '''
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                powershell '''
                docker build -t test-docker-project:django .
                docker tag test-docker-project:django 676327216025.dkr.ecr.us-east-1.amazonaws.com/test-docker-project:latest
                '''
            }
        }

        stage('Push Image to ECR') {
            steps {
                powershell '''
                docker push 676327216025.dkr.ecr.us-east-1.amazonaws.com/test-docker-project:latest
                '''
            }
        }
    }
}
