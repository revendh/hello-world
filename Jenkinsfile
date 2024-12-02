pipeline {
    agent any
    
    stages { 
        stage('SCM Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/revendh/hello-world.git'
            }
        }
        
        // Run SonarQube analysis
        stage('Run Sonarqube') {
            environment {
                scannerHome = tool 'SonarQubeScanner';
            }
            steps {
                withSonarQubeEnv(credentialsId: 'sonar_token', installationName: 'SonarQubeServer') {
                    sh """
                    ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=MyApp \
                        -Dsonar.projectName=MyApp \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=.
                    """
                }
            }
        }
    }
}
