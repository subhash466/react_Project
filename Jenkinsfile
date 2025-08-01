pipeline {
    agent any

    environment {
        REPO_URL = 'https://github.com/subhash466/react_Project.git'
        BRANCH = 'main'
        CREDENTIALS_ID = 'my_tocken'

        CLIENT_DIR = 'client'
        SERVER_DIR = 'server'

        CLIENT_DEPLOY_DIR = '/var/www/html'
        SERVER_PORT = '5000'
    }

    stages {
        stage('📥 Checkout Code') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/${BRANCH}"]],
                    userRemoteConfigs: [[
                        url: "${REPO_URL}",
                        credentialsId: "${CREDENTIALS_ID}"
                    ]]
                ])
            }
        }

        // === FRONTEND STAGES ===
        stage('📦 Install Frontend Dependencies') {
            steps {
                dir("${CLIENT_DIR}") {
                    sh 'npm install'
                }
            }
        }

        stage('⚙️ Build React App') {
            steps {
                dir("${CLIENT_DIR}") {
                    // Avoid CI=true issues
                    sh 'CI=false npm run build'
                }
            }
        }

        stage('🚀 Deploy React App') {
            steps {
                script {
                    sh "rm -rf ${CLIENT_DEPLOY_DIR}/*"
                    sh "cp -r ${CLIENT_DIR}/build/* ${CLIENT_DEPLOY_DIR}/"
                }
            }
        }

        // === BACKEND STAGES ===
        stage('📦 Install Backend Dependencies') {
            steps {
                dir("${SERVER_DIR}") {
                    sh 'npm install'
                }
            }
        }

        stage('🚀 Start Backend Server with PM2') {
            steps {
                dir("${SERVER_DIR}") {
                    sh 'pm2 delete all || true' // Clear old process
                    sh 'pm2 start index.js --name backend-app'
                }
            }
        }
    }

    post {
        success {
            echo "✅ Client and Server deployed successfully!"
        }
        failure {
            echo "❌ Build or Deploy failed!"
        }
    }
}
