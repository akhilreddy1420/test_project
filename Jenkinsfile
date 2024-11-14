pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/akhilreddy1420/test_project.git'
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
                            -Dsonar.host.url=http://132.145.193.30:9000
                        """
                    }
                }
            }
        }
        
        stage('Deploy to Apache') {
            steps {
                script {
                    sh 'sudo cp -r /var/www/html /var/www/html_backup || true'
                    
                    sh '''
                        sudo rm -rf /var/www/html/*
                        sudo cp -r * /var/www/html/
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
