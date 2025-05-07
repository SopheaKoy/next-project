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

    environment {
        // This will be loaded later from the file
    }

    stages {
        stage('Load ENV') {
            steps {
                withCredentials([file(credentialsId: 'env-config', variable: 'ENV_FILE')]) {
                    sh '''
                    echo "Loading env vars from $ENV_FILE..."
                    set -a
                    source "$ENV_FILE"
                    set +a
                    '''
                }
            }
        }

        stage('Use ENV') {
            steps {
                sh 'echo "Environment: $ENV"'
                sh 'echo "Port: $PORT"'
            }
        }
    }
}
