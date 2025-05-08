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
                sh 'docker --version'    // Ensure Docker is installed
                sh 'docker info'         // Check Docker info
                sh 'echo "FROM alpine" > Dockerfile'  // Creates a new Dockerfile (adjust if needed)
                sh 'docker build -t test-alpine .'  // Builds a Docker image using the current Dockerfile
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
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
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
            sh 'docker logout'
        }
    }
}
