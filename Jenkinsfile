def CLIENT_CHANGED = false
def SERVER_CHANGED = false

pipeline {
    agent any

    environment {
        REACT_BUILD_DIR = 'client/build'
        DEPLOY_DIR = '/var/www/html' // Change this if needed
    }

    stages {

        stage('🔍 Check Changed Folders') {
            steps {
                script {
                    def changeLog = sh(
                        script: 'git diff --name-only HEAD~1 HEAD',
                        returnStdout: true
                    ).trim()
                    
                    CLIENT_CHANGED = changeLog.contains('client/')
                    SERVER_CHANGED = changeLog.contains('server/')
                    
                    echo "Client Changed: ${CLIENT_CHANGED}"
                    echo "Server Changed: ${SERVER_CHANGED}"
                }
            }
        }

        // -------- CLIENT --------

        stage('📦 Install Client Dependencies') {
            when { expression { CLIENT_CHANGED } }
            steps {
                dir('client') {
                    sh 'npm install'
                }
            }
        }

        stage('⚙️ Build React App') {
            when { expression { CLIENT_CHANGED } }
            steps {
                dir('client') {
                    sh 'npm run build'
                }
            }
        }

        stage('🚀 Deploy React App') {
            when { expression { CLIENT_CHANGED } }
            steps {
                sh 'rm -rf ${DEPLOY_DIR}/*'
                sh 'cp -r ${REACT_BUILD_DIR}/* ${DEPLOY_DIR}/'
            }
        }

        // -------- SERVER --------

        stage('📦 Install Server Dependencies') {
            when { expression { SERVER_CHANGED } }
            steps {
                dir('server') {
                    sh 'npm install'
                }
            }
        }

        stage('🚀 Start/Restart Node Server') {
            when { expression { SERVER_CHANGED } }
            steps {
                dir('server') {
                    sh 'pm2 restart server-app || pm2 start index.js --name server-app'
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Deployment Success!'
        }
        failure {
            echo '💥 Build or Deployment Failed!'
        }
    }
}
