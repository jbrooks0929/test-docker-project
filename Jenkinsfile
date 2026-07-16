pipeline {
    agent any

   environment {
    AWS_REGION = 'us-east-1'
    ECR_REGISTRY = '676327216025.dkr.ecr.us-east-1.amazonaws.com'
    ECR_REPO = 'test-docker-project'
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
        stage('Debug AWS') {
    steps {
        withAWS(region: "${AWS_REGION}", credentials: 'aws-creds') {
            powershell '''
            Write-Host "Testing AWS identity..."
            aws sts get-caller-identity

            Write-Host "Testing ECR token..."
            $token = aws ecr get-login-password --region us-east-1

            Write-Host "Token length:"
            $token.Length
            '''
        }
    }
}

        stage('Login to ECR') {
    steps {
        withAWS(region: "${AWS_REGION}", credentials: 'aws-creds') {
            bat '''
            aws ecr get-login-password --region us-east-1 > ecr_password.txt

            docker login ^
            --username AWS ^
            --password-stdin ^
            676327216025.dkr.ecr.us-east-1.amazonaws.com < ecr_password.txt

            del ecr_password.txt
            '''
        }
    }
}

        stage('Docker Test') {
    steps {
        bat '''
        whoami
        docker version
        docker info
        '''
    }
}
        stage('Build Docker Image') {
    steps {
        powershell '''
        $ErrorActionPreference = "Stop"

        docker build -t $env:IMAGE_NAME .

        docker tag $env:IMAGE_NAME `
        $env:ECR_REGISTRY/$env:ECR_REPO:latest
        '''
    }
}

       stage('Push Image to ECR') {
    steps {
        powershell '''
        $ErrorActionPreference = "Stop"

        docker push $env:ECR_REGISTRY/$env:ECR_REPO:latest
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
