pipeline {
    agent none

    environment {
        DOCKER_IMAGE = 'y3ko/jenkins:test3'
    }

    stages {
        stage('Build and Push Docker Image') {
            agent {
                kubernetes {
                    defaultContainer 'jnlp'
                    yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: docker-builder
spec:
  containers:
  - name: docker
    image: docker:19.03.12-dind
    env:
    - name: DOCKER_HOST
      value: tcp://192.168.1.120:2375
    securityContext:
      privileged: true
    volumeMounts:
    - name: docker-graph-storage
      mountPath: /var/lib/docker
  volumes:
  - name: docker-graph-storage
    emptyDir: {}
"""
                }
            }
            steps {
                container('docker') {
                    script {
                        // Docker imajını derle.
                        def dockerImage = docker.build("y3ko/jenkins:test3")
                        // Docker Registry'ye giriş yap ve imajı push et.
                        withDockerRegistry(credentialsId: "dockerhub", url: "") {
                            dockerImage.push()
                        }
                    }
                }
            }
        }

        stage('Deploy Docker Image') {
            steps {
                script {
                    // SSH üzerinden remote sunucuya bağlan ve docker container'ı çalıştır
                    sshPublisher(publishers: [
                        sshPublisherDesc(
                            configName: 'ssh',
                            transfers: [
                                sshTransfer(
                                    execCommand: """
                                    docker pull y3ko/jenkins:test3 &&
                                    docker stop myapp || true &&
                                    docker rm myapp || true &&
                                    docker run -d --name myapp -p 80:80 y3ko/jenkins:test3
                                    """,
                                    execTimeout: 120000,
                                    usePty: true,
                                    remoteDirectory: "/home/test"
                                )
                            ]
                        )
                    ])
                }
            }
        }
    }
}
