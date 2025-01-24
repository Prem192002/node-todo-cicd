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
                git 'https://github.com/your-github-username/your-repo.git'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }
        
        stage('Push to AWS ECR') {
            steps {
                sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $REPO_URL'
                sh 'docker tag $IMAGE_NAME:latest $REPO_URL'
                sh 'docker push $REPO_URL'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['EC2_SSH_CREDENTIALS']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@your-ec2-public-ip <<EOF
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
