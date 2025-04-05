pipeline {
    agent any
    stages {
        stage('Cleanup Artifacts') {
            steps {
                script {
                    def maxAgeDays = 7
                    sh """
                        find /var/artifacts -type d -mtime +${maxAgeDays} -exec rm -rf {} \\;
                    """
                }
            }
        }
    }
}