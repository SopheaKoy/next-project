pipeline {
    agent any

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

                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                                -Dsonar.projectKey=my_project_key \
                                -Dsonar.sources=. \
                                -Dsonar.host.url=${SONAR_HOST_URL} \
                                -Dsonar.login=${SONAR_TOKEN}
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