pipeline {
    agent any

    environment {
        IMAGE_NAME = "sanketyelugotla/nginx-cont"
        TAG = "latest"
        CONTAINER_NAME = "nginx-cont"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/sanketyelugotla/jenkins-nginx'
            }
        }

        stage('Test Docker') {
            steps {
                bat 'docker --version'    // Check Docker version
                bat 'docker info'         // Check Docker info
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t $IMAGE_NAME:$TAG ."  // Corrected Docker build command
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKER_CREDENTIALS', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    bat "docker push $IMAGE_NAME:$TAG"  // Push image to Docker Hub
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying the Docker container...'
                    bat """
                        docker stop ${CONTAINER_NAME} || exit 0
                        docker rm ${CONTAINER_NAME} || exit 0
                        docker run -d -p 8081:80 --name ${CONTAINER_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}
                    """
                }
            }
        }
    }

    post {
        always {
            bat 'docker logout'  // Logout from Docker Hub
        }
    }
}
