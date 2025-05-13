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
                    env.CAN_DEPLOY = "false"
                    env.TARGET_ENV = "none"

                    def autoDeployBranches = ['prod', 'staging', 'uat']

                    if (env.BRANCH_NAME == 'dev') {
                        echo "Branch is 'dev' — manual deployment required"
                        env.CAN_DEPLOY = params.MANUAL_DEPLOY ? "true" : "false"
                        env.TARGET_ENV = "dev"

                        if (env.CAN_DEPLOY == "false") {
                            echo "⚠️  Manual deployment blocked. Please check MANUAL_DEPLOY=true in the Jenkins UI."
                        }

                    } else if (autoDeployBranches.contains(env.BRANCH_NAME)) {
                        echo "Branch '${env.BRANCH_NAME}' — auto-deploy enabled."
                        env.CAN_DEPLOY = "true"

                        switch (env.BRANCH_NAME) {
                            case 'prod':
                                env.TARGET_ENV = "prod"
                                break
                            case 'staging':
                                env.TARGET_ENV = "staging"
                                break
                            case 'uat':
                                env.TARGET_ENV = "uat"
                                break
                        }
                    } else {
                        echo "🛑 Branch '${env.BRANCH_NAME}' is not allowed to deploy. Skipping deployment."
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