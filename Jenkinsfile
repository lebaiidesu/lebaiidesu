pipeline {
    agent any

    environment {
        SERVERS = "ubuntu@172.31.9.92 ubuntu@172.31.35.152"
        DOCROOT = "/var/www/html"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Deploy') {
            steps {
                sshagent(credentials: ['webservers-ssh-key']) {
                    sh '''
                    for SERVER in $SERVERS
                    do
                      rsync -avz --delete \
                      --rsync-path="sudo rsync" \
                      ./ $SERVER:$DOCROOT

                      ssh $SERVER "sudo systemctl reload apache2"
                    done
                    '''
                }
            }
        }
    }
}
