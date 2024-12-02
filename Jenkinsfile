pipeline {
    agent any

    environment {
        SONARQUBE_SCANNER = tool 'SonarQubeScanner' // Replace with your configured tool name
        SONARQUBE_SERVER = 'SonarQubeServer' // Replace with your configured server name
    }

    stages {
        stage('Checkout') {
            steps {
                git credentialsId: 'revendh_github_creds', url: 'https://github.com/revendh/hello-world.git', branch: 'master'
            }
        }

        stage('Build') {
            steps {
                script {
                    def mvnHome = tool 'Maven' // Replace 'Maven' with the configured name in Jenkins
                    sh "${mvnHome}/bin/mvn clean compile -pl server -am" // Compile the "server" module
                }
            }
        }

        stage('Run SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    sh """
                    ${SONARQUBE_SCANNER}/bin/sonar-scanner \
                        -Dsonar.projectKey=MyApp \
                        -Dsonar.sources=. \
                        -Dsonar.java.binaries=server/target/classes \
                        -Dsonar.host.url=http://10.45.88.133:9000 \
                        -Dsonar.login=****** // Replace with your token
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
            echo 'Pipeline failed! Check the logs for details.'
        }
        success {
            echo 'Pipeline succeeded!'
        }
    }
}
