pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'YOUR_GITHUB_REPO_URL'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -Dsonar.projectKey=my-website \
                            -Dsonar.sources=. \
                            -Dsonar.host.url=http://localhost:9000
                        """
                    }
                }
            }
        }
        
        stage('Deploy to Apache') {
            steps {
                script {
                    // Backup existing files
                    sh 'sudo cp -r /var/www/html /var/www/html_backup || true'
                    
                    // Deploy new files
                    sh '''
                        sudo rm -rf /var/www/html/*
                        sudo cp -r * /var/www/html/
                        sudo chown -R www-data:www-data /var/www/html
                        sudo chmod -R 755 /var/www/html
                    '''
                }
            }
        }
    }
    
    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed!'
            sh 'sudo cp -r /var/www/html_backup/* /var/www/html/ || true'
        }
    }
}
