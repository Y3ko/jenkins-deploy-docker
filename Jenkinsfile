pipeline {
    agent none

    environment {
        DOCKER_IMAGE = 'y3ko/jenkins:test3'
    }

    stages {
        stage('Deploy Docker Image') {
            agent any
            environment {
                remoteCommands = """
                    java --version;
                    java --version;
                    java --version
                """
            }
            steps {
                script {
                    sshagent(['ssh']) {
                        sh "ssh -tt nfs@192.168.1.121 '${remoteCommands}'"
                    }
                }
            }
        }
    }
}
