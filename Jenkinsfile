pipeline {
    agent any

    environment {
        NODE_ENV = 'production'
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

        stage('📦 Install Frontend Dependencies') {
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

        stage('📦 Install Backend Dependencies') {
            steps {
                dir('server') {
                    sh 'npm install'
                }
            }
        }

        stage('🚀 Start Backend via PM2') {
            steps {
                dir('server') {
                    sh '''
                        pm2 delete all || true
                        pm2 start index.js --name backend-app --update-env
                        pm2 save
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ CI/CD pipeline ran successfully.'
        }
        failure {
            echo '❌ CI/CD pipeline failed.'
        }
    }
}
