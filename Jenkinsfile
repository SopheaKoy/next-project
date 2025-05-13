pipeline {
    agent any

    parameters {
        booleanParam(name: 'RUN_PIPELINE', defaultValue: false, description: 'Check to manually run Build + Deploy + Notify')
        string(name: 'TARGET_BRANCH', defaultValue: 'dev-sophea', description: 'Branch to deploy from')
        // choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'production'], description: 'Deployment environment')
        // string(name: 'NOTIFY_EMAIL', defaultValue: '', description: 'Email address for notifications (optional)')
    }

    options {
        skipDefaultCheckout()
        timeout(time: 60, unit: 'MINUTES')
        disableConcurrentBuilds()
        // timestamps()
    }

    stages {
        stage('Build') {
            when {
                expression { return params.RUN_PIPELINE }
            }
            steps {
                echo '============= Build ====================='
                echo "Building for environment: ${params.ENVIRONMENT}"
                echo "Branch name: ${params.TARGET_BRANCH}"
                sh 'echo "Building the application..."'
                // Add actual build steps here based on your application
                // For example:
                // sh 'npm install'
                // sh 'npm run build'
                echo '============= END ====================='
            }
        }

        stage('Tests') {
            when {
                expression { return params.RUN_PIPELINE }
            }
            steps {
                echo '============= Testing ====================='
                sh 'echo "Running tests..."'
                // Add actual test commands here
                // sh 'npm test'
                echo '============= END ====================='
            }
        }

        stage('Deploy') {
            when {
                expression { return params.RUN_PIPELINE }
            }
            steps {
                echo '============= Deploy ====================='
                echo "Deploying to ${params.ENVIRONMENT} environment"
                echo "Branch name: ${params.TARGET_BRANCH}"
                
                script {
                    // Environment-specific deployment logic
                    switch(params.ENVIRONMENT) {
                        case 'dev':
                            sh 'echo "Deploying to development server..."'
                            // sh './deploy.sh dev'
                            break
                        case 'staging':
                            sh 'echo "Deploying to staging server..."'
                            // sh './deploy.sh staging'
                            break
                        case 'production':
                            input message: 'Approve production deployment?', ok: 'Deploy'
                            sh 'echo "Deploying to production server..."'
                            // sh './deploy.sh production'
                            break
                    }
                }
                echo '============= END ====================='
            }
        }

        stage('Notify') {
            when {
                expression { return params.RUN_PIPELINE }
            }
            steps {
                echo '============= Notify ====================='
                script {
                    def deploymentStatus = currentBuild.result ?: 'SUCCESS'
                    echo "Deployment status: ${deploymentStatus}"
                    
                    if (params.NOTIFY_EMAIL) {
                        echo "Sending notification email to: ${params.NOTIFY_EMAIL}"
                    }    
                }
                echo '============= END ====================='
            }
        }
    }

    post {
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Please check the logs for details."
        }
        always {
            echo "Cleaning up workspace..."
            cleanWs()
        }
    }
}