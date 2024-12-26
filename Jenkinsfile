pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "react-app:latest" // Image name and tag
        DOCKER_COMPOSE_PATH = "./docker-compose.yml" // Path to docker-compose.yml
        NEXUS_CREDENTIALS = credentials('nexus-cred') // Jenkins credential ID for Nexus
        SONARQUBE_TOKEN = credentials('react-app') // Jenkins credential ID for SonarQube token
        NEXUS_REPO_URL = '54.244.211.2:8081/repository/react-app1/' // Nexus repository URL
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
                    withSonarQubeEnv(SONARQUBE_SERVER) { // Use the configured SonarQube server ID
                        def scannerHome = tool 'sonar' // Use the configured SonarScanner tool name
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
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
                    env.DOCKER_IMAGE_VERSIONED = "${NEXUS_REPO_URL}react-app:${timestamp}"
                    sh "docker tag ${DOCKER_IMAGE} ${DOCKER_IMAGE_VERSIONED}"
                }
            }
        }

       stage('Push Docker Image to Nexus') {
    steps {
        script {
            // Fetch the credentials and use them
            withCredentials([usernamePassword(credentialsId: 'nexus-cred', usernameVariable: 'NEXUS_USER', passwordVariable: 'NEXUS_PASS')]) {
                // Ensure that the version is properly set
                def dockerImage = "54.244.211.2:8081/repository/react-app1/react-app:${DOCKER_IMAGE_VERSIONED}"
                sh """
                    docker push ${dockerImage}
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
