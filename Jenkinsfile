pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-swarm-app:latest'
        DOCKER_REGISTRY = 'mohdsalmanirfan' 
        SERVER_IP = '172.27.96.1'  //ipconfig     IPv4 Address. . . . . . . . . . . : 172.27.96.1
        SERVER_USER = 'salman\salma'  //whoami
        SSH_KEY = credentials('dockerid') // Add your SSH key in Jenkins credentials
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo-url.git'
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'dotnet build --configuration Release'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'dotnet test'
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    sh 'dotnet publish --configuration Release --output ./out'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    sh "docker login -u ${DOCKER_REGISTRY} -p ${DOCKER_PASSWORD}" // Set DOCKER_PASSWORD in Jenkins credentials
                    sh "docker tag ${DOCKER_IMAGE} ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
                    sh "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy to Docker Swarm') {
            steps {
                script {
                    sshagent(['your-ssh-key-id']) { // Use the SSH key added in Jenkins credentials
                        sh """
                        ssh -o StrictHostKeyChecking=no ${SERVER_USER}@${SERVER_IP} '
                            cd /path/to/your/app
                            docker service update --image ${DOCKER_REGISTRY}/${DOCKER_IMAGE} my-swarm-service
                        '
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean workspace after the build
        }
    }
}