pipeline {
    agent any

    environment {
        CLIENT_DIR = 'client'
        SERVER_DIR = 'server'
    }

    stages {
        stage('📥 Checkout Code') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/main']],
                    userRemoteConfigs: [[
                        url: 'https://github.com/subhash466/react_Project.git',
                        credentialsId: 'my_tocken'
                    ]]
                ])
            }
        }

        stage('📦 Install Backend') {
            steps {
                dir("${SERVER_DIR}") {
                    sh 'npm install'
                }
            }
        }

        stage('📦 Install Frontend + Build') {
            steps {
                dir("${CLIENT_DIR}") {
                    sh 'npm install'
                    sh 'npm run build'
                }
            }
        }

        stage('🚀 PM2 Restart Server + Client') {
            steps {
                sh '''
                # Restart Backend
                if pm2 describe chat_server > /dev/null; then
                    pm2 restart chat_server
                else
                    pm2 start server/index.js --name chat_server
                fi

                # Restart Frontend
                if pm2 describe react_client > /dev/null; then
                    pm2 restart react_client
                else
                    pm2 start "npx serve -s client/build" --name react_client
                fi

                # Save PM2 process list
                pm2 save
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Chat app deployed!'
        }
        failure {
            echo '❌ Build failed — check logs.'
        }
    }
}
