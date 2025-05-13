pipeline {
    agent any

    parameters {
        // Manual deploy checkbox
        booleanParam(name: 'MANUAL_DEPLOY', defaultValue: false, description: 'Check to manually trigger deployment for dev-sophea branch')
    }

    options {
        skipDefaultCheckout(false)
        timeout(time: 60, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    stages {
        stage('Determine Deployment Type') {
            steps {
                script {
                    // Initialize default deployment settings
                    env.CAN_DEPLOY = "false"
                    env.TARGET_ENV = "none"

                    // Check if branch is dev-sophea (requires manual deployment)
                    if (env.BRANCH_NAME == 'dev-sophea') {
                        echo "Branch is 'dev-sophea' — manual deployment required"
                        env.CAN_DEPLOY = params.MANUAL_DEPLOY ? "true" : "false"
                        env.TARGET_ENV = "development"

                        if (env.CAN_DEPLOY == "false") {
                            echo "⚠️  Manual deployment blocked. Please check MANUAL_DEPLOY=true in the Jenkins UI."
                        }
                    }
                    // Auto-deploy for other branches like dev, main, etc.
                    else {
                        echo "Branch '${env.BRANCH_NAME}' — auto-deploy triggered."
                        env.CAN_DEPLOY = "true"
                        env.TARGET_ENV = "staging"  // or production, depending on the branch
                    }

                    echo "CAN_DEPLOY: ${env.CAN_DEPLOY}"
                    echo "TARGET_ENV: ${env.TARGET_ENV}"
                }
            }
        }

        stage('Deploy') {
            when {
                expression { return env.CAN_DEPLOY == "true" }
            }
            steps {
                echo '============= Deploy ====================='
                echo "Branch name: ${env.BRANCH_NAME}"
                echo "Deploying to ${env.TARGET_ENV} environment"
                // Add actual deployment commands here (e.g., docker, kubernetes, etc.)
                echo '============= END ====================='
            }
        }

        stage('Notify') {
            when {
                expression { return env.CAN_DEPLOY == "true" }
            }
            steps {
                echo '============= Notify ====================='
                script {
                    def deploymentStatus = currentBuild.result ?: 'SUCCESS'
                    echo "Deployment status: ${deploymentStatus} to ${env.TARGET_ENV}"
                }
                echo '============= END ====================='
            }
        }
    }

    post {
        success {
            script {
                if (env.CAN_DEPLOY == "true") {
                    echo "Pipeline executed successfully with deployment!"
                } else {
                    echo "Pipeline executed successfully (without deployment)"
                }
            }
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