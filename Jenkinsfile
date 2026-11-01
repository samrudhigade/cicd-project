
pipeline {
    agent any

    environment {
        WEB_SERVER = 'WEB_SERVER_PRIVATE_IP'
        DEPLOY_USER = 'deploy'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code from GitHub'
                checkout scm
            }
        }

        stage('Validate') {
            steps {
                echo 'Validating website files'
                sh '''
                    test -f website/index.html
                    test -f website/style.css
                '''
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging website files'
                sh '''
                    tar -czf website.tar.gz -C website .
                '''
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: ['webserver-deploy-key']) {
                    sh '''
                        scp -o StrictHostKeyChecking=no website.tar.gz ${DEPLOY_USER}@${WEB_SERVER}:/tmp/website.tar.gz

                        ssh -o StrictHostKeyChecking=no ${DEPLOY_USER}@${WEB_SERVER} "sudo tar -xzf /tmp/website.tar.gz -C /var/www/html"
                    '''
                }
            }
        }

        stage('Verify') {
            steps {
                sh '''
                    curl -f http://${WEB_SERVER}/
                '''
            }
        }
    }
}
