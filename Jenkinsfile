pipeline {
    agent any
    environment {
        DB_HOST = 'localhost'
        DB_NAME = 'sharedappdb'
        DB_USER = 'devops'
        DB_PASS = 'password'
        ARTIFACT_DIR = '/var/artifacts'
        MAX_AGE_DAYS = 7
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Olakenny1975/Flask-Node.js.git'
            }
        }
        stage('Build Flask App') {
            steps {
                dir('flask-app') {
                    sh 'python3 -m venv venv'
                    sh './venv/bin/pip install -r requirements.txt'
                }
            }
        }
        stage('Build Node.js App') {
            steps {
                dir('node-app') {
                    sh 'npm install'
                }
            }
        }
        stage('Deploy Apps') {
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'EC2',
                            transfers: [
                                sshTransfer(
                                    sourceFiles: 'flask-app/**,node-app/**',
                                    remoteDirectory: '/var/www'
                                )
                            ],
                            usePromotionTimestamp: true
                        )
                    ]
                )
                sh '''
                    ssh ec2-user@18.224.150.121 "sudo systemctl restart flask-app"
                    ssh ec2-user@18.224.150.121 "pm2 restart node-app"
                '''
            }
        }
        stage('Cleanup Artifacts') {
            steps {
                script {
                    sh """
                        find ${ARTIFACT_DIR} -type d -mtime +${MAX_AGE_DAYS} -exec rm -rf {} \\;
                    """
                }
            }
        }
    }
}