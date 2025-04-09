pipeline {
    agent any
    environment {
        FLASK_APP_PATH = '/opt/flask-app'
        NODE_APP_PATH = '/opt/node-app'
        FLASK_PORT = 5000
        NODE_PORT = 3000
        EC2_HOST = '18.221.114.251:'   
        EC2_USER = 'ssh -i sulaiman.pem ec2-user@18.221.114.251:'       
    }
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/Olakenny1975/Flask-Node.js.git'  
            }
        }
        stage('Build Flask App') {
            steps {
                script {
                    sh 'pip install -r requirements.txt'  
                }
            }
        }
        stage('Build Node.js App') {
            steps {
                script {
                    sh 'npm install'  
                }
            }
        }
        stage('Deploy Flask App') {
            steps {
                script {
                    sh """
                    ssh ${ec2-user@18.221.114.251} << EOF
                    cd ${FLASK_APP_PATH}
                    export FLASK_APP=app.py
                    nohup python3 ${FLASK_APP_PATH}/app.py --host=0.0.0.0 --port=${FLASK_PORT} > flask_app.log 2>&1 &
                    EOF
                    """
                }
            }
        }
        stage('Deploy Node.js App') {
            steps {
                script {
                    sh """
                    ssh ${ec2-user@18.221.114.251} << EOF
                    cd ${NODE_APP_PATH}
                    pm2 start ${NODE_APP_PATH}/app.js --name node-app --port=${NODE_PORT}
                    EOF
                    """
                }
            }
        }
        stage('Test Deployment') {
            steps {
                script {
                    sh "curl http://${ec2-user@18.221.114.251:${FLASK_PORT}"  // Check Flask app
                    sh "curl http://${ec2-user@18.221.114.251}:${NODE_PORT}"   // Check Node.js app
                }
            }
        }
        stage('Clean Up Old Artifacts') {
            steps {
                script {
                    // Delete artifacts older than X days
                    def daysOld = 30
                    def buildDir = "/var/jenkins_home/jobs/your-job-name/builds"
                    sh "find ${buildDir} -type f -mtime +${daysOld} -exec rm -f {} \\;"
                }
            }
        }
    }
    post {
        always {
            echo 'Cleaning up after build'
            // Clean up any temporary files or perform final steps after the build
        }
    }
}