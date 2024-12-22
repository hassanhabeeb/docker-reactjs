pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "react-app:latest" // Image name and tag
        DOCKER_COMPOSE_PATH = "./docker-compose.yml"
        NEXUS_CREDENTIALS = credentials('nexus-cred') // Jenkins credential ID for Nexus credentials
        SONARQUBE_TOKEN = credentials('react-app') // Jenkins credential ID for SonarQube token
        NEXUS_REPO_URL = 'http://54.244.211.2:8081/repository/react-app/'
        SONARQUBE_SERVER = 'sonar' // SonarQube server ID in Jenkins
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'master', url: 'git@github.com:hassanhabeeb/docker-reactjs.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv(SONARQUBE_SERVER) {
                        sh """
                            ${tool 'SonarScanner'}/bin/sonar-scanner \
                            -Dsonar.projectKey=react-app \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://35.162.77.248:9000 \
                            -Dsonar.login=${SONARQUBE_TOKEN}
                        """
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker-compose -f ${DOCKER_COMPOSE_PATH} build"
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                script {
                    def timestamp = new Date().format('yyyyMMddHHmmss')
                    env.DOCKER_IMAGE_VERSIONED = "react-app:${timestamp}"
                    sh "docker tag react-app:latest ${DOCKER_IMAGE_VERSIONED}"
                }
            }
        }

        stage('Push Docker Image to Nexus') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'nexus-cred', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                        sh """
                            echo ${NEXUS_PASS} | docker login -u ${NEXUS_USER} --password-stdin ${NEXUS_REPO_URL}
                            docker push ${NEXUS_REPO_URL}${DOCKER_IMAGE_VERSIONED}
                        """
                    }
                }
            }
        }

        stage('Run Application') {
            steps {
                script {
                    sh "docker-compose -f ${DOCKER_COMPOSE_PATH} up -d"
                }
            }
        }
    }
}
