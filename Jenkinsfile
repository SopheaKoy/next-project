pipeline {
    agent any

    parameters {
        booleanParam(name: 'MANUAL_DEPLOY', defaultValue: false, description: 'Check this to manually trigger deployment')
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
                    env.CAN_DEPLOY = "false"
                    env.TARGET_ENV = "none"

                    // Handle manual deployment for 'dev-sophea' branch
                    if (env.BRANCH_NAME == 'dev-sophea') {
                        echo "Branch is 'dev-sophea' — manual deployment required"
                        env.CAN_DEPLOY = params.MANUAL_DEPLOY ? "true" : "false"
                        env.TARGET_ENV = "development"

                        if (env.CAN_DEPLOY == "false") {
                            echo "⚠️  Manual deployment blocked. Please run with MANUAL_DEPLOY=true"
                        }
                    } 
                    // Handle 'master' branch deployment
                    else if (env.BRANCH_NAME == 'master' && params.MANUAL_DEPLOY) {
                        echo "Branch is 'master' — manual deployment triggered"
                        env.CAN_DEPLOY = "true"
                        env.TARGET_ENV = "production"
                    }
                    // Default case when not a deployable branch
                    else {
                        echo "Branch '${env.BRANCH_NAME}' is not configured for deployment."
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
