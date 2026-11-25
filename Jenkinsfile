pipeline {
    agent any

      environment {
        DOCKERHUB_USER = 'bayramalaya'
        PATH = "C:\\Users\\slimb\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin;${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/bayramalaya1-hue/TP4.git'
            }
        }

        stage('Build images') {
            steps {
                bat 'docker build -t %DOCKERHUB_USER%/mern-server:latest ./Server'
                bat 'docker build -t %DOCKERHUB_USER%/mern-client:latest ./Client'
            }
        }

        stage('Push Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub',
                                 usernameVariable: 'DOCKER_USER',
                                 passwordVariable: 'DOCKER_PASS')]) {
                    bat 'echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin'
                    bat 'docker push %DOCKERHUB_USER%/mern-server:latest'
                    bat 'docker push %DOCKERHUB_USER%/mern-client:latest'
                }
            }
        }

        stage('Deploy (compose)') {
            steps {
                bat 'docker compose down'
                bat 'docker compose up -d --build'
            }
        }
    }

    post {
        always {
            bat 'docker logout'
        }
    }
}