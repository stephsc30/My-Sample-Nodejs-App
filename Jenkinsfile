pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "your-dockerhub-username/your-nodejs-app"
        DOCKER_REGISTRY_CREDENTIALS = 'docker-credentials-id'
        EC2_SSH_CREDENTIALS = 'ec2-ssh-credentials-id'
        REMOTE_EC2_IP = 'your-ec2-instance-public-ip'
    }
    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/your-username/your-nodejs-app-repo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE)
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: DOCKER_REGISTRY_CREDENTIALS, usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                    script {
                        docker.withRegistry('https://index.docker.io/v1/', DOCKER_REGISTRY_CREDENTIALS) {
                            docker.image(DOCKER_IMAGE).push()
                        }
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(credentials: [EC2_SSH_CREDENTIALS]) {
                    sh """
                    ssh -o StrictHostKeyChecking=no ec2-user@${REMOTE_EC2_IP} 'docker pull ${DOCKER_IMAGE}:latest && docker run -d -p 80:3000 ${DOCKER_IMAGE}'
                    """
                }
            }
        }
    }
}
