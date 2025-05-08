pipeline {
    agent any

    environment {
        IMAGE_NAME = "sanketyelugotla/nginx-cont"
        TAG = "latest"
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
                    bat "docker run -p 8082:80 -d $IMAGE_NAME:$TAG"
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
