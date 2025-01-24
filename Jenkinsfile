pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID = '992382487320'
        AWS_REGION = 'ap-south-1'
        IMAGE_NAME = 'myapp'
        REPO_URL = '992382487320.dkr.ecr.ap-south-1.amazonaws.com/myapp'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/Prem192002/node-todo-cicd.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }
        
        stage('Push to AWS ECR') {
            steps {
                sh 'aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 992382487320.dkr.ecr.ap-south-1.amazonaws.com'
                sh 'docker tag myapp:latest 992382487320.dkr.ecr.ap-south-1.amazonaws.com/myapp:latest'
                sh 'docker push 992382487320.dkr.ecr.ap-south-1.amazonaws.com/myapp:latest'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['EC2_SSH_CREDENTIALS']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@13.200.235.213 <<EOF
                    docker pull $REPO_URL
                    docker stop myapp || true
                    docker rm myapp || true
                    docker run -d --name myapp -p 80:5000 $REPO_URL
                    EOF
                    '''
                }
            }
        }
    }
}
