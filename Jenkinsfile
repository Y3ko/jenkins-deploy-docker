pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'y3ko/jenkins:test3'
    }
    //öncesine docker image build edilip dockerhub'a atılabilir
    stages {
        stage('Deploy via SSH') {
            steps {
                script {
                    // SSH sunucusunu kullanarak komut çalıştır
                    sshPublisher(
                        publishers: [
                            sshPublisherDesc(
                                configName: 'nfs', // Jenkins UI'da tanımladığınız SSH sunucusunun adı
                                transfers: [
                                    sshTransfer(
                                        execCommand: 'docker pull y3ko/test && docker run -d --name myapp -p 80:80 y3ko/test' // Uzak sunucuda çalıştırılacak komut
                                    )
                                ],
                                usePromotionTimestamp: false,
                                useWorkspaceInPromotion: false,
                                verbose: true
                            )
                        ]
                    )
                }
            }
        }
    }
}
