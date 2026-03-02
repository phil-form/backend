pipeline {
    agent any

    environment {
        SSH_SERVER = credentials('ssh-server')
        SSH_KEY_CREDENTIALS_ID = 'prod-server-key'
        DEPLOY_PATH = credentials('deployment-prod')
    }

    stages {
        stage('Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }

        stage("Build container") {
            steps {
                // !!!! Attention !!!! : Assurez-vous que :
                // 1. Docker est installé et configuré sur votre machine Jenkins.
                // 2. Votre Jenkins a les permissions nécessaires pour exécuter des commandes Docker.
                sh 'docker --version'
                // On supprime l'image existante pour éviter les conflits.
                sh 'docker image rm -f deployment || true'
                sh 'docker build -t deployment .'
                // Exporter l'image
                sh 'docker save deployment -o ./deployment.tar'
            }
        }

// Test push trigger webhook
// Test push trigger webhook
        stage('Deploy SSH') {
            steps {
               sshagent([env.SSH_KEY_CREDENTIALS_ID]) {
                    sh '''
                        scp ./deployment.tar $SSH_SERVER:$DEPLOY_PATH/
                        ssh $SSH_SERVER "
                            cd $DEPLOY_PATH
                            docker compose stop back || true
                            docker compose rm back || true
                            docker image rm deployment || true
                            docker load -i deployment.tar
                            docker compose up back -d
                        "
                    '''
               }
            }
        }

        stage('Trigger front pipeline') {
            steps {
                build job: 'prod',
                            wait: false,
                            propagate: false
            }
        }
    }

    post {
        always {
            sh 'docker image rm -f deployment-front || true'
            sh 'rm ./deployment-front.tar || true'
        }
    }
}