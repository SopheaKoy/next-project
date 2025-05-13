pipeline {
    agent any

    parameters {
        booleanParam(name: 'MANUAL_DEPLOY', defaultValue: false, description: 'Check to manually trigger deployment for branches that require approval')
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
                    // Set environment variables for deployment configuration
                    env.AUTO_DEPLOY = "false"
                    env.CAN_DEPLOY = "false"
                    env.TARGET_ENV = "unknown"
                    
                    // Branches that auto-deploy
                    if (env.BRANCH_NAME == 'dev-sophea' || env.BRANCH_NAME =~ /^feature\/.*$/) {
                        echo "Branch '${env.BRANCH_NAME}' is configured for automatic deployment"
                        env.AUTO_DEPLOY = "true"
                        env.CAN_DEPLOY = "true"
                        env.TARGET_ENV = "development"
                    } 
                    // Branches that require manual approval
                    else if (env.BRANCH_NAME == 'dev-sophea' || env.BRANCH_NAME == 'main' || env.BRANCH_NAME =~ /^release\/.*$/) {
                        echo "Branch '${env.BRANCH_NAME}' requires manual deployment approval"
                        env.AUTO_DEPLOY = "false"
                        env.CAN_DEPLOY = params.MANUAL_DEPLOY ? "true" : "false"
                        
                        if (env.BRANCH_NAME == 'master' || env.BRANCH_NAME == 'main') {
                            env.TARGET_ENV = "production"
                        } else {
                            env.TARGET_ENV = "staging"
                        }
                        
                        if (env.CAN_DEPLOY == "false") {
                            echo "To deploy this branch, run the build with MANUAL_DEPLOY=true parameter"
                        }
                    } 
                    // Not a deployable branch
                    else {
                        echo "Branch '${env.BRANCH_NAME}' is not configured for deployment"
                        env.AUTO_DEPLOY = "false"
                        env.CAN_DEPLOY  = "false"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                echo '============= Build ====================='
                echo "Branch name: ${env.BRANCH_NAME}"
                sh 'echo "Building the application..."'
                echo '============= END ====================='
            }
        }

        stage('Tests') {
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