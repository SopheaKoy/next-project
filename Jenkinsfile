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

// pipeline {
//     agent any

//     stages {
//         stage('Load Config') {
//             steps {
//                 // Use the correct File ID 'd06af51c-803a-47c8-a46b-9a77828ae001' for the app_config
//                 configFileProvider([configFile(fileId: 'd06af51c-803a-47c8-a46b-9a77828ae001', variable: 'CONFIG_FILE')]) {
//                     script {
//                         // Print the filename (path where the file is stored in workspace)
//                         echo "The config file is located at: ${CONFIG_FILE}"
                        
//                         // Optionally, you can also read the file if needed
//                         def props = readProperties file: "${CONFIG_FILE}"
//                         echo "Port: ${props['PORT']}"
//                         echo "Environment: ${props['ENV']}"
//                         echo "Database Host: ${props['DB_HOST']}"
//                     }
//                 }
//             }
//         }
//     }
// }



pipeline {
    agent any

    
    stages {
        stage('Build') {
            when {
                anyOf {
                    branch 'dev'
                    branch 'dev-sophea'
                }
            }

            environment {
                DOCKER_HOST = 'unix:///var/run/docker.sock'
                DOCKER_BUILDKIT = '1'
            }
            steps {
                script {
                    echo 'Running build stage...'
                    sh '''
                        apk add --no-cache git ca-certificates curl
                        update-ca-certificates
                        docker info
                        echo "$DOCKER_REGISTRY_PASSWORD" | docker login $DOCKER_REGISTRY -u "$DOCKER_REGISTRY_USER" --password-stdin
                        git config --global http.$CI_SERVER_HOST.extraheader "PRIVATE-TOKEN:$PERSONAL_ACCESS_TOKEN"
                        chmod +x check_changes.sh
                        ./check_changes.sh
                        source build.env
                        if [ "$REBUILD_NEEDED" = "true" ]; then
                            chmod +x build.sh;
                            ./build.sh;
                        else
                            echo "Skipping build as no changes detected in Dockerfile or requirements.txt";
                        fi
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Add your deploy commands here
                    echo '============= Deploy ====================='
                    echo "Branch name: ${env.GIT_BRANCH}"
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

