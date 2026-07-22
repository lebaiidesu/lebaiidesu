pipeline {
    agent any

    environment {
        AZ_ACCOUNT = credentials('azure-storage-account')
        ACI_URL = 'http://stagingaci2026jlf.centralindia.azurecontainer.io'
        AZ_SHARE = 'website-share'
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
                        az storage file upload \
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
                echo 'Staging validation passed.'
                echo 'Deploying to AWS production servers...'
            }
        }

    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }

        failure {
            echo 'Staging validation failed. Production deployment blocked.'
        }
    }
}
