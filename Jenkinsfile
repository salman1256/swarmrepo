pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'my-swarm-app:latest'
        DOCKER_REGISTRY = 'mohdsalmanirfan' 
        SERVER_IP = '172.27.96.1'  // Replace with your actual server IP
        SERVER_USER = 'salman'  // Use just the username, not the domain
        SSH_KEY = credentials('dockerid') // Add your SSH key in Jenkins credentials
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/salman1256/swarmrepo.git' // Replace with your actual repo URL
            }
        }

        stage('Build') {
            steps {
                script {
                    bat 'dotnet build --configuration Release'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    bat 'dotnet test'
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    bat 'dotnet publish --configuration Release --output ./out'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    bat "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    
                    bat "docker tag ${DOCKER_IMAGE} ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
                    bat "docker push ${DOCKER_REGISTRY}/${DOCKER_IMAGE}"
                }
            }
        }

       stage('Deploy to Docker Swarm') {
        steps {
            script {
                sshagent(['dockerid']) { // Use the SSH key added in Jenkins credentials
                    bat """
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