pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "stephsc30/nodejs-app:${env.BUILD_ID}"  // Use BUILD_ID for unique tagging
        DOCKER_REGISTRY_CREDENTIALS = credentials('docker-hub-credentials')
        EC2_DEPLOY_USER = 'ubuntu'
        EC2_IP = '34.229.61.77'
        GITHUB_CREDENTIALS = credentials('GitHub-Credentials')
        SSH_CREDENTIALS_ID = 'ec2-ssh-key'  // Ensure SSH credentials ID is added here
    }

    stages {
        stage('Checkout') {
            steps {
                // GitHub checkout using credentials (HTTPS)
                git url: 'https://github.com/stephsc30/My-Sample-Nodejs-App.git', credentialsId: 'GitHub-Credentials'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Login to Docker Hub
                    sh "echo ${DOCKER_REGISTRY_CREDENTIALS.PASSWORD} | docker login -u ${DOCKER_REGISTRY_CREDENTIALS.USERNAME} --password-stdin"

                    // Build the Docker image
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }

        stage('Push to Docker Registry') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    sh "docker push $DOCKER_IMAGE"
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    // Deploy the Docker image to EC2 instance
                    sshagent([SSH_CREDENTIALS_ID]) {
                        sh """
                            ssh -o StrictHostKeyChecking=no $EC2_DEPLOY_USER@$EC2_IP '
                                docker pull $DOCKER_IMAGE &&
                                docker stop myapp || true &&
                                docker rm myapp || true &&
                                docker run -d -p 80:8080 --name myapp $DOCKER_IMAGE
                            '
                        """
                    }
                }
            }
        }
    }
}
