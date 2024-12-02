pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER = tool 'SonarQubeScanner' // Update with your SonarQube Scanner tool name in Jenkins
        SONARQUBE_SERVER = 'SonarQubeServer' // Update with your SonarQube server configuration name
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
                    // Make sure your project is built and compiled before SonarQube analysis.
                    // If using Maven, add `mvn clean install` here.
                    sh 'mvn clean compile'
                }
            }
        }

        stage('Run SonarQube Analysis') {
            environment {
                // Ensure SonarQube analysis environment variables are injected
                SONARQUBE_ENV = credentials('sonar_token') // Adjust to your Jenkins credentials setup
            }
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh """
                    ${SONARQUBE_SCANNER}/bin/sonar-scanner \
                        -Dsonar.projectKey=MyApp \
                        -Dsonar.projectName=MyApp \
                        -Dsonar.projectVersion=1.0 \
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
            echo 'Cleaning up workspace...'
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
