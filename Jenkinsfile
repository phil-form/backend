pipeline {
    agent {
        docker {
            image 'node:24-alpine'
        }
    }

    environment {
        DB_HOST = '127.0.0.1'
        DB_USER = 'db_user'
        DB_PASSWORD = 'db_password'
        DB_NAME = 'db_name'
    }

    stages {
        stage('Install dependecies') {
            steps {
                sh 'node --version'
                sh 'npm --version'
                sh 'npm install'
            }
        }
        
        stage('Start application') {
            steps {
                sh 'npm start'
            }
        }
    }
}