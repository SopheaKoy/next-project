pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'nextjs-app'
    }

    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image for the Next.js app...'
                    sh 'docker-compose build'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    echo 'Running tests inside the Docker container...'
                    // You can run your tests in a Docker container here, e.g., with 'docker exec'
                    sh 'docker-compose run nextjs npm test'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying the Next.js app...'
                    sh 'docker-compose up -d'
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
