pipeline {
    agent {
        docker { image 'node:20-slim' }  // Use the Node.js Docker image
    }

    stages {
        stage('Install Dependencies') {
            steps {
                script {
                    // Run npm install to install dependencies
                    sh 'npm install'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run npm test to execute tests
                    sh 'npm test'
                }
            }
        }
    }

    post {
        success {
            echo 'Build and tests were successful!'
        }
        failure {
            echo 'Build or tests failed. Please check the logs.'
        }
    }
}
