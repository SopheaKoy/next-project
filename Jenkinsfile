pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    echo 'Building the project...'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    echo 'Running tests...'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying the project...'
                }
            }
        }
    }

    post {
        success {
            echo 'Build, tests, and deployment were successful!'
        }
        failure {
            echo 'Build, tests, or deployment failed. Please check the logs.'
        }
    }
}
