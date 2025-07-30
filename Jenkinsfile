pipeline {
    agent any

    environment {
        CLIENT_DIR = 'client'
        SERVER_DIR = 'server'
        CI = 'false' // Prevent React treating warnings as errors
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
                # Install serve globally if not found
                if ! command -v serve &> /dev/null
                then
                    echo "🔧 Installing serve globally..."
                    npm install -g serve
                fi

                # Start/Restart Backend
                pm2 describe chat_server > /dev/null
                if [ $? -eq 0 ]; then
                    echo "🔁 Restarting backend..."
                    pm2 restart chat_server
                else
                    echo "🚀 Starting backend..."
                    pm2 start server/index.js --name chat_server
                fi

                # Start/Restart Frontend
                pm2 describe react_client > /dev/null
                if [ $? -eq 0 ]; then
                    echo "🔁 Restarting frontend..."
                    pm2 restart react_client
                else
                    echo "🚀 Starting frontend..."
                    pm2 start "serve -s client/build -l 5000" --name react_client
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
