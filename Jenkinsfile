pipeline {
    agent any
    environment {
        SONARQUBE_SERVER = 'SonarQube'
        SONAR_HOST_URL = 'http://10.30.212.54:9000'
        SONAR_AUTH_TOKEN = credentials('sonar_token')
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv("${env.SONARQUBE_SERVER}") {
                        withCredentials([string(credentialsId: 'sonar_token', variable: 'SONAR_AUTH_TOKEN')]) {
                            def scannerHome = tool 'SonarQubeScanner'
                            sh """
                                ${scannerHome}/bin/sonar-scanner \
                                    -Dsonar.projectKey=PipelineScan \
                                    -Dsonar.sources=. \
                                    -Dsonar.host.url=${SONAR_HOST_URL} \
                                    -Dsonar.token=${SONAR_AUTH_TOKEN}
                            """
                        }
                    }
                }
            }
        }
        stage('Quality Gate') {
            steps {
                // Esperar el resultado del Quality Gate
                timeout(time: 1, unit: 'HOURS') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}
