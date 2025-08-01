pipeline {
    agent any

    environment {
        NODE_HOME = '/usr/bin'
        PM2_HOME = '/home/sm/.pm2'
        PATH = "${NODE_HOME}:${PATH}"
        REPO_URL = 'https://github.com/subhash466/react_Project.git'
        GIT_CREDENTIALS = 'my_tocken'
    }

    stages {
        stage('📥 Checkout Code') {
            steps {
                git credentialsId: "${GIT_CREDENTIALS}", url: "${REPO_URL}", branch: 'main'
            }
        }

        stage('📦 Install Client Dependencies') {
            steps {
                dir('client') {
                    sh 'npm install'
                }
            }
        }

        stage('🛠️ Build React App') {
            steps {
                dir('client') {
                    sh 'CI=false npm run build'
                }
            }
        }

        stage('🚀 Deploy React App') {
            steps {
                sh '''
                    sudo rm -rf /var/www/html/*
                    sudo cp -r client/build/* /var/www/html/
                '''
            }
        }

        stage('📦 Install Server Dependencies') {
            steps {
                dir('server') {
                    sh 'npm install'
                }
            }
        }

        stage('🔁 Restart Backend using PM2') {
            steps {
                dir('server') {
                    sh '''
                        export PM2_HOME=/home/sm/.pm2
                        /usr/local/bin/pm2 delete backend-app || true
                        /usr/local/bin/pm2 start index.js --name backend-app
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Monorepo deployment (Client + Server) successful!'
        }
        failure {
            echo '❌ Deployment failed. Check logs.'
        }
    }
}
