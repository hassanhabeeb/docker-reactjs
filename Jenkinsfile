pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "react-app:latest" // Image name and tag
        DOCKER_COMPOSE_PATH = "./docker-compose.yml"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master', url: 'git@github.com:hassanhabeeb/docker-reactjs.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        // Build Docker image using docker-compose
                        sh "docker-compose -f ${DOCKER_COMPOSE_PATH} build"
                    } catch (Exception e) {
                        error "Failed to build Docker image: ${e.message}"
                    }
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    try {
                        // Run the application
                        sh "docker-compose -f ${DOCKER_COMPOSE_PATH} up -d"
                    } catch (Exception e) {
                        error "Failed to run application: ${e.message}"
                    }
                }
            }
        }

        stage('Test Application') {
            steps {
                script {
                    try {
                        // Replace with your actual testing steps
                        sh 'curl -I http://localhost'
                    } catch (Exception e) {
                        error "Application testing failed: ${e.message}"
                    }
                }
            }
        }
    }
}
