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
                bat 'docker --version'    // Check Docker version on Windows
                bat 'docker info'         // Check Docker info on Windows
                bat 'echo "FROM alpine" > Dockerfile'  // Create Dockerfile
                bat 'docker build -t test-alpine .'  // Build Docker image
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Builds the Docker image
                    docker.build("${IMAGE_NAME}:${TAG}", ".")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKER_CREDENTIALS', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    // Log in to Docker Hub
                    bat "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    docker.image("${IMAGE_NAME}:${TAG}").push()
                }
            }
        }
    }

    post {
        always {
            // Log out of Docker Hub
            bat 'docker logout'
        }
    }
}
