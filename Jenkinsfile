pipeline {
    agent any

    environment {
        // Nombre del servidor SonarQube configurado en Jenkins
        SONARQUBE_SERVER = 'SonarQube'
        SONAR_HOST_URL = '<http://localhost:9000>' // URL de tu servidor SonarQube
        SONAR_AUTH_TOKEN = credentials('sonarqube-token') // ID de la credencial en Jenkins
        // Agregar sonar-scanner al PATH
        PATH = "/opt/sonar-scanner/bin:${env.PATH}"
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
                withSonarQubeEnv("${SONARQUBE_SERVER}") {
                    // Ejecutar el análisis con SonarScanner
                    sh '''
                        sonar-scanner \
                        -Dsonar.projectKey=Pipeline_SonarQube \
                        -Dsonar.sources=vulnerabilities \
                        -Dsonar.host.url=$={SONAR_HOST_URL} \
                        -Dsonar.login=$={SONAR_AUTH_TOKEN} \
                        -Dsonar.php.version=8.0
                    '''
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
