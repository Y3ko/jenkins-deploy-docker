pipeline {
    agent none

    environment {
        DOCKER_IMAGE = 'y3ko/jenkins:test3'
    }

        stage('Deploy Docker Image') {
            agent any
            steps {
                script {
                        sh '''
                            mkdir -p /home/jenkins/.ssh
                            chmod 700 /home/jenkins/.ssh
                            touch /home/jenkins/.ssh/known_hosts
                            chmod 644 /home/jenkins/.ssh/known_hosts
                        '''
                    // SSH anahtarları ile güvenli bir şekilde bağlanmak için sshagent adımını kullan.
                    sh 'ssh-keyscan -H 192.168.1.119 >> ~/.ssh/known_hosts'
                    sshagent(['ssh']) {
                        sh """
                            ssh root@192.168.1.119 'docker pull ${DOCKER_IMAGE} &&
                            docker stop myapp || true &&
                            docker rm myapp || true &&
                            docker run -d --name myapp -p 80:80 ${DOCKER_IMAGE}'
                        """
                    }
                }
            }
        }
    }
}
