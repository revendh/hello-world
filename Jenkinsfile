pipeline {
    agent any

    environment {
        // Define environment variables for tools and configurations
        SONARQUBE_SCANNER = tool 'SonarQubeScanner' // Update this with your configured SonarQube scanner tool name in Jenkins
        SONARQUBE_SERVER = 'SonarQubeServer'       // Update this with your configured SonarQube server name in Jenkins
        MAVEN_HOME = tool 'Maven'                 // Update 'Maven' with the exact name of Maven in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Checking out source code from Git repository...'
                git branch: 'master', url: 'https://github.com/revendh/hello-world.git', credentialsId: 'revendh_github_creds'
            }
        }

        stage('Build') {
            steps {
                echo 'Running Maven build...'
                sh "${MAVEN_HOME}/bin/mvn clean compile"
            }
        }

        stage('Run SonarQube Analysis') {
            steps {
                echo 'Running SonarQube static code analysis...'
                withSonarQubeEnv('SonarQubeServer') {
                    sh """
                    ${SONARQUBE_SCANNER}/bin/sonar-scanner \
                        -Dsonar.projectKey=MyApp \
                        -Dsonar.sources=. \
                        -Dsonar.java.binaries=target/classes \
                        -Dsonar.host.url=$SONAR_HOST_URL \
                        -Dsonar.login=${SONAR_AUTH_TOKEN}
                    """
                }
            }
        }

        stage('Quality Gate') {
            steps {
                echo 'Waiting for SonarQube quality gate result...'
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
            deleteDir() // Deletes the workspace after the pipeline
        }
        success {
            echo 'Pipeline succeeded!'
        }
        failure {
            echo 'Pipeline failed! Check the logs for details.'
        }
    }
}
