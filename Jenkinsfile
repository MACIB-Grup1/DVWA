pipeline {
    agent any

    environment {
        // Nombre del servidor SonarQube configurado en Jenkins
        SONARQUBE_SERVER = 'SonarQube'
        SONAR_HOST_URL = 'http://sonarqube:9000' // URL de tu servidor SonarQube
        SONAR_AUTH_TOKEN = credentials('sonar_token') // ID de la credencial en Jenkins
        // SONAR_AUTH_TOKEN = 'sqa_d90d740085f9d4ba6b5f91e3455914479c00add4'
        // Agregar sonar-scanner al PATH
        // PATH = "/opt/sonar-scanner/bin:${env.PATH}"
    }

    

    stages {
        stage('Checkout') {
            steps {
                // Clonar el código fuente desde el repositorio
                checkout scm
            }
        }
        stage('SonarQube Analysis') {
            steps {
                // Configurar el entorno de SonarQube
                withSonarQubeEnv("${env.SONARQUBE_SERVER}") {
                    // Ejecutar el análisis con SonarScanner
                    sh '''
                        printenv
                        sonar-scanner
                        -Dsonar.projectKey=PipelineScan
                        -Dsonar.sources=.
                        -Dsonar.host.url=${env.SONAR_HOST_URL}
                        -Dsonar.token=${env.SONAR_AUTH_TOKEN}
                    '''
                    //-Dsonar.token=${sqa_d90d740085f9d4ba6b5f91e3455914479c00add4}
                    //-Dsonar.login=${env.SONAR_AUTH_TOKEN}
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
