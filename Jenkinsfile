pipeline {
    agent any

    parameters {
        booleanParam(name: 'RUN_PIPELINE', defaultValue: false, description: 'Check to manually run Build + Deploy + Notify')
    }

    options {
        skipDefaultCheckout()
    }

    stages {
        stage('Guard') {
            when {
                not {
                    allOf {
                        branch 'dev-sophea'
                        expression { return params.RUN_PIPELINE }
                    }
                }
            }
            steps {
                script {
                    error("This pipeline only runs manually on 'dev-sophea' with RUN_PIPELINE=true.")
                }
            }
        }

        stage('Build') {
            when {
                allOf {
                    branch 'dev-sophea'
                    expression { return params.RUN_PIPELINE }
                }
            }
            steps {
                echo '============= Build ====================='
                echo "Branch name: ${env.BRANCH_NAME}"
                echo 'Building the application...'
                echo '============= END ====================='
            }
        }

        stage('Deploy') {
            when {
                allOf {
                    branch 'dev-sophea'
                    expression { return params.RUN_PIPELINE }
                }
            }
            steps {
                echo '============= Deploy ====================='
                echo "Branch name: ${env.BRANCH_NAME}"
                echo 'Deploying application...'
                echo '============= END ====================='
            }
        }

        stage('Notify') {
            when {
                allOf {
                    branch 'dev-sophea'
                    expression { return params.RUN_PIPELINE }
                }
            }
            steps {
                echo '============= Notify ====================='
                echo 'Sending notifications...'
                echo '============= END ====================='
            }
        }
    }
}
