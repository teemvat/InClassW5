pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQubeServer'
        DOCKER_IMAGE = 'teemvat/otp-inclass-w5'
        DOCKER_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                git branch: 'master', url: 'https://github.com/ADirin/sep2_week5_inclass_s2.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv("${SONARQUBE_SERVER}") {
                        bat "mvn clean verify sonar:sonar -Dsonar.login=%SONAR_TOKEN%"
                    }
                }
            }
        }
        
        stage('Docker Build & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-hub-creds',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    bat """
                        docker build -t %DOCKER_IMAGE%:%DOCKER_TAG% .
                        echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                        docker push %DOCKER_IMAGE%:%DOCKER_TAG%
                    """
                }
            }
        }
    }
}
