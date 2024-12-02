pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER = tool 'SonarQubeScanner' // Replace with your configured tool name
        SONARQUBE_SERVER = 'SonarQubeServer' // Replace with your configured server name
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'revendh_github_creds', url: 'https://github.com/revendh/hello-world.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                script {
                    def mvnHome = tool 'Maven' // Replace 'Maven' with the configured name in Jenkins
                    sh "${mvnHome}/bin/mvn clean compile"
                }
            }
        }

        stage('Run SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh """
                    ${SONARQUBE_SCANNER}/bin/sonar-scanner \
                        -Dsonar.projectKey=CarWarehouse \
                        -Dsonar.sources=. \
                        -Dsonar.java.binaries=target/classes
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    def qualityGate = waitForQualityGate()
                    if (qualityGate.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qualityGate.status}"
                    }
                }
            }
        }
    }

    post {
        always {
            deleteDir()
        }
        failure {
            echo 'Pipeline failed!'
        }
        success {
            echo 'Pipeline succeeded!'
        }
    }
}
