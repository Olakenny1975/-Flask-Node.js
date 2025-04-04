pipeline {
    agent any
    
    environment {
        POSTGRES_USER = 'your_db_user'
        POSTGRES_PASSWORD = 'your_db_password'
        POSTGRES_DB = 'your_db_name'
        EC2_SERVER = 'your-ec2-server-ip'
        DEPLOY_DIR_FLASK = '/path/to/deploy/flask-app'
        DEPLOY_DIR_NODE = '/path/to/deploy/node-app'
    }
    
    stages {
        stage('Clone Repositories') {
            steps {
                script {
                    // Clone Flask app repo
                    git url: 'https://your-flask-app-repo.git', branch: 'main'
                    
                    // Clone Node.js app repo
                    git url: 'https://your-node-app-repo.git', branch: 'main'
                }
            }
        }
        
        stage('Setup Flask Application') {
            steps {
                script {
                    // Setup Flask app environment
                    sh 'cd $DEPLOY_DIR_FLASK && pip3 install -r requirements.txt'
                    sh 'cd $DEPLOY_DIR_FLASK && python3 app.py'  // Adjust with your Flask app's start command
                }
            }
        }
        
        stage('Setup Node.js Application') {
            steps {
                script {
                    // Setup Node.js app environment
                    sh 'cd $DEPLOY_DIR_NODE && npm install'
                    sh 'cd $DEPLOY_DIR_NODE && npm start'  // Adjust with your Node.js app's start command
                }
            }
        }
        
        stage('Database Setup') {
            steps {
                script {
                    // You may want to run database migrations or setup here, if needed
                    // For example:
                    sh 'psql -U $POSTGRES_USER -d $POSTGRES_DB -c "CREATE TABLE IF NOT EXISTS users (id SERIAL PRIMARY KEY, name TEXT);"'
                }
            }
        }

        stage('Cleanup Old Artifacts') {
            steps {
                script {
                    // Clean up old artifacts from previous builds (optional)
                    sh 'rm -rf $DEPLOY_DIR_FLASK/old_artifacts/*'
                    sh 'rm -rf $DEPLOY_DIR_NODE/old_artifacts/*'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    // SSH into EC2 and run the deployment steps
                    sh '''
                        ssh -i /path/to/your/private-key.pem ec2-user@$EC2_SERVER << 'EOF'
                            # Pull the latest code and restart services if needed
                            cd $DEPLOY_DIR_FLASK && git pull && sudo systemctl restart flask-service
                            cd $DEPLOY_DIR_NODE && git pull && sudo systemctl restart node-service
                        EOF
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo 'Deployment was successful!'
        }
        failure {
