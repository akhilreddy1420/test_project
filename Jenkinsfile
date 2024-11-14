pipeline {
     agent { label 'linux' }

    environment {
        REMOTE_SERVER = '132.145.193.30'  
        REMOTE_USER = 'root'             
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/akhilreddy1420/test_project.git'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarQube'
                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=my-website \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://132.145.193.30:9000
                            -Dsonar.login=admin \
                            -Dsonar.password=admin@123
                        """
                    }
                }
            }
        }

        stage('Deploy to Apache') {
            steps {
                script {
                    sh 'ssh ${REMOTE_USER}@${REMOTE_SERVER} "sudo cp -r /var/www/html /var/www/html_backup || true"'

                    sh """
                        scp -r * ${REMOTE_USER}@${REMOTE_SERVER}:/var/www/html/
                    """
                    
                    sh """
                        ssh ${REMOTE_USER}@${REMOTE_SERVER} '
                            sudo chown -R www-data:www-data /var/www/html/ &&
                            sudo chmod -R 755 /var/www/html/ &&
                            sudo systemctl restart apache2
                        '
                    """
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    sh """
                        ssh ${REMOTE_USER}@${REMOTE_SERVER} '
                            sudo systemctl status apache2
                        '
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment and analysis were successful!'
        }
        failure {
            echo 'Deployment or analysis failed! Restoring backup...'
            sh 'ssh ${REMOTE_USER}@${REMOTE_SERVER} "sudo cp -r /var/www/html_backup/* /var/www/html/ || true"'
        }
    }
}

