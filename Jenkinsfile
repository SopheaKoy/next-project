// pipeline {
//     agent any

//     environment {
//         DOCKER_IMAGE = 'nextjs-app'
//         PROJECT_NAME = 'jenkins_test'
//         VERSION = '1.0.0'
//         SERVER_IP = '192.168.0.1'
//         SERVICE_URL = 'http://example.com/service'
//     }

//     stages {
//         stage('Build Docker Image') {
//             steps {
//                 script {
//                     echo 'Building Docker image for the Next.js app...'
//                     sh 'docker-compose build'
//                 }
//             }
//         }

//         stage('Run Tests') {
//             steps {
//                 script {
//                     echo 'Running tests inside the Docker container...'
//                     // Skip tests if no test script is available
//                     sh 'docker-compose run nextjs npm test || true'
//                 }
//             }
//         }

//         stage('Deploy') {
//             steps {
//                 script {
//                     echo 'Deploying the Next.js app...'
//                     sh 'docker-compose up -d'
//                 }
//             }
//         }
//     }

//    post {
//         success {
//             script {
//                 def commit_date = sh(script: 'date', returnStdout: true).trim()
//                 def pipeline_duration = currentBuild.durationString
//                 def pipeline_status = 'SUCCESS'
//                 def commit_message = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
//                 def commit_hash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()

//                 def message = "<code>
// $(get_status_emoji("${currentBuild.currentResult}")) ${escapeHtml("${env.PROJECT_NAME}")}, ${escapeHtml("${env.CI_COMMIT_REF_NAME}")}, ${escapeHtml("${env.GITLAB_USER_NAME}")}, ${escapeHtml("${env.commit_date}")}
// -----------------------------
// Pipeline Overview
// ENVIRONMENT : <b>${env.PROJECT_NAME}</b>
// -----------------------------
// </code>
//                 "
//                 // Send the formatted message via Telegram using curl
//                 sh """
//                 curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage" \
//                 -d chat_id=${env.TELEGRAM_CHAT_ID} \
//                 -d text="${message}" \
//                 -d parse_mode="HTML"
//                 """
//             }
//         }
//         failure {
//             script {
//                 def pipeline_status = 'FAILED'
//                 def commit_date = sh(script: 'date', returnStdout: true).trim()
//                 def pipeline_duration = currentBuild.durationString
//                 def commit_message = sh(script: 'git log -1 --pretty=%B', returnStdout: true).trim()
//                 def commit_hash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()

//                 // Compose the failure message with HTML
//                 def message = "<code>
// $(get_status_emoji("${currentBuild.currentResult}")) ${escapeHtml("${env.PROJECT_NAME}")}, ${escapeHtml("${env.CI_COMMIT_REF_NAME}")}, ${escapeHtml("${env.GITLAB_USER_NAME}")}, ${escapeHtml("${env.commit_date}")}
// -----------------------------
// Pipeline Overview
// ENVIRONMENT : <b>${env.PROJECT_NAME}</b>
// -----------------------------
// </code>
//                 "
//                 // Send the formatted message via Telegram using curl
//                 sh """
//                 curl -s -X POST "https://api.telegram.org/bot${env.TELEGRAM_BOT_TOKEN}/sendMessage" \
//                 -d chat_id=${env.TELEGRAM_CHAT_ID} \
//                 -d text="${message}" \
//                 -d parse_mode="HTML"
//                 """
//             }
//         }
//     }
// }

// // Helper function to return emoji based on status
// def get_status_emoji(status) {
//     return status == 'SUCCESS' ? '✅' : '❌'
// }

pipeline {
    agent any
    stages {
        stage('Deploy') {
            steps {
                configFileProvider([configFile(fileId: '0c7f8326-8717-4be3-9e7c-818f1396b316', variable: 'ENV_CONFIG')]) {
                    script {
                        def props = readProperties file: "${ENV_CONFIG}"

                        // Convert keys like db.host → db_host to make them valid shell variables
                        def exportLines = props.collect { k, v -> "export ${k.replace('.', '_')}='${v}'" }.join('\n')

                        // Write the export statements to a file
                        writeFile file: 'export-env.sh', text: exportLines

                        // Use the exported env variables in your shell script
                        sh '''
                            source export-env.sh
                            echo "Database Host: $db_host"
                            echo "API URL: $api_url"
                            echo "Monitoring Enabled: $monitor_enabled"

                            ./deploy.sh \
                              --db-host=$db_host \
                              --db-user=$db_username \
                              --schema=$db_schema \
                              --api-url=$api_url \
                              --api-version=$api_version
                        '''
                    }
                }
            }
        }
    }
}
