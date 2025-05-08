pipeline {
    agent any

    tools {
        // Change from sonarScanner to SonarRunnerInstallation
        SonarRunnerInstallation 'SonarScanner' // This should match the name in Global Tool Configuration
    }

    environment {
        SONAR_TOKEN = credentials('sonarqube-token') // Secure token from Jenkins credentials
    }

    stages {
        stage('Build and SonarQube Analysis') {
            when {
                anyOf {
                    branch 'dev'
                    branch 'dev-sophea'
                }
            }
            steps {
                script {
                    echo "Building and analyzing on branch: ${env.BRANCH_NAME}"

                    withSonarQubeEnv('SonarQube') {
                        sh """
                            sonar-scanner \
                                -Dsonar.projectKey=my_project_key \
                                -Dsonar.sources=. \
                                -Dsonar.login=$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage('Quality Gate') {
            when {
                anyOf {
                    branch 'dev'
                    branch 'dev-sophea'
                }
            }
            steps {
                timeout(time: 3, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}