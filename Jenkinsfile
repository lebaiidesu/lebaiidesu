pipeline {
    agent any

    environment {
        PATH = "/usr/bin:/usr/local/bin:${env.PATH}"
        AZ_ACCOUNT = credentials('azure-storage-account')
        AZ_SHARE = 'website-share'
        ACI_URL = 'http://stagingaci2026jlf.centralindia.azurecontainer.io'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy-Staging') {
            steps {
                withCredentials([
                    string(credentialsId: 'azure-storage-key', variable: 'AZ_KEY')
                ]) {
                    sh '''
                        /usr/bin/az storage file upload \
                            --account-name "$AZ_ACCOUNT" \
                            --account-key "$AZ_KEY" \
                            --share-name "$AZ_SHARE" \
                            --source index.html
                    '''
                }
            }
        }

        stage('Test-Staging') {
            steps {
                sh '''
                    curl -f $ACI_URL > output.html
                    grep "Version 2" output.html
                '''
            }
        }

        stage('Deploy-Production') {
            steps {
                echo 'Deploying to AWS Production'
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Staging validation failed'
            echo 'Production deployment blocked'
        }

        always {
            echo 'Pipeline finished'
        }
    }
}
