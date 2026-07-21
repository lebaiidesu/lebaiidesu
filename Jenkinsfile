pipeline {
    agent any

    environment {
        AZ_ACCOUNT = 'levistore20260721'
        AZ_SHARE = 'webcontent'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy to Azure File Share') {
            steps {
                withCredentials([
                    string(credentialsId: 'azure-storage-key', variable: 'AZ_KEY')
                ]) {
                    sh '''
                        az storage file upload-batch \
                          --account-name "$AZ_ACCOUNT" \
                          --account-key "$AZ_KEY" \
                          --destination "$AZ_SHARE" \
                          --source . \
                          --no-progress
                    '''
                }
            }
        }

    }
}
