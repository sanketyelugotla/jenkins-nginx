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

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${IMAGE_NAME}:${TAG}", ".")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'DOCKER_CREDENTIALS', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.image("${IMAGE_NAME}:${TAG}").push()
                }
            }
        }
    }

    post {
        always {
            sh 'docker logout'
        }
    }
}
