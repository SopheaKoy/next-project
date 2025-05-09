pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    // Add your deploy commands here
                    echo '============= Build ====================='
                    echo "Branch name: ${env.GIT_BRANCH}"
                    echo 'Deploying the application...'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Add your deploy commands here
                    echo '============= Deploy ====================='
                    echo "Branch name: ${env.BRANCH_NAME}"
                    echo 'Deploying the application...'
                }
            }
        }


        stage('Notify') {
            steps {
                script {
                    echo '============= Notify ====================='
                    echo 'Sending notifications...'
                    // Example: You can send email, Slack, etc.
                    // For example, using Slack plugin:
                }
            }
        }
    }
}