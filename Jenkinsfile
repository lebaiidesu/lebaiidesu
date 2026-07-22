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
                        /usr/bin/az storage file upload-batch \
                            --account-name "$AZ_ACCOUNT" \
                            --account-key "$AZ_KEY" \
                            --destination "$AZ_SHARE" \
                            --source . \
                            --no-progress
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
                echo "Staging validation successful"
                echo "Deploying to AWS Production Servers"

                /*
                Real production deployment example:

                scp index.html ubuntu@WEBSERVER1:/var/www/html/index.html
                scp index.html ubuntu@WEBSERVER2:/var/www/html/index.html
                */

                echo "Deployment completed"
            }
        }

    }

    post {

        success {
            echo "Pipeline completed successfully"
        }

        failure {
            echo "Staging validation failed"
            echo "Production deployment blocked"
        }

        always {
            cleanWs()
        }
    }
}
