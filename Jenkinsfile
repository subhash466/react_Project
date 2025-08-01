pipeline {
    agent any

    environment {
        PM2_HOME = "/home/sm/.pm2"   // 👈 Set PM2_HOME to sm user path
        CLIENT_DIR = "client"
        SERVER_DIR = "server"
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

        stage('📦 Install Client Dependencies') {
            steps {
                dir("${CLIENT_DIR}") {
                    sh 'npm install'
                }
            }
        }

        stage('🛠️ Build React App') {
            steps {
                dir("${CLIENT_DIR}") {
                    sh 'CI=false npm run build'
                }
            }
        }

        stage('🚀 Deploy React App') {
            steps {
                sh '''
                    sudo rm -rf /var/www/html/* || true
                    sudo cp -r ${CLIENT_DIR}/build/* /var/www/html/
                '''
            }
        }

        stage('📦 Install Server Dependencies') {
            steps {
                dir("${SERVER_DIR}") {
                    sh 'npm install'
                }
            }
        }

        stage('🧠 Start Backend with PM2') {
            steps {
                dir("${SERVER_DIR}") {
                    sh '''
                        pm2 delete all || true
                        pm2 start index.js --name backend-app
                        pm2 save
                    '''
                }
            }
        }

    }

    post {
        success {
            echo '✅ Deployment completed successfully.'
        }
        failure {
            echo '❌ Deployment failed.'
        }
    }
}
