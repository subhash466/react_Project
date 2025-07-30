pipeline {
    agent any

    environment {
        CLIENT_DIR = 'client'
        SERVER_DIR = 'server'
        CI = 'false'
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
                    sh 'npm install serve'  // ✅ Local install
                    sh 'npm run build'
                }
            }
        }

        stage('🚀 PM2 Restart Server + Client') {
            steps {
                sh '''
                # Start/Restart Backend
                pm2 describe chat_server > /dev/null
                if [ $? -eq 0 ]; then
                    pm2 restart chat_server
                else
                    pm2 start server/index.js --name chat_server
                fi

                # Start/Restart Frontend using local "serve"
                FRONTEND_SERVE="./client/node_modules/.bin/serve"

                pm2 describe react_client > /dev/null
                if [ $? -eq 0 ]; then
                    pm2 restart react_client
                else
                    $FRONTEND_SERVE -s client/build -l 5000 &
                    pm2 start $FRONTEND_SERVE --name react_client -- start -s client/build -l 5000
                fi

                pm2 save
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployed Successfully!'
        }
        failure {
            echo '❌ Deployment Failed. Check logs.'
        }
    }
}

