pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "react-app:latest" // Image name and tag
        DOCKER_COMPOSE_PATH = "./docker-compose.yml"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'git@github.com:hassanhabeeb/docker-reactjs.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker-compose build'
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    sh 'docker-compose up -d'
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    // Replace with your testing steps
                    sh 'curl -I http://localhost'
                }
            }
        }
    }

    post {
        always {
            echo 'Cleaning up resources...'
            sh 'docker-compose down --remove-orphans'
        }
    }
}
