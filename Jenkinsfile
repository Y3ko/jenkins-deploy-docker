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
                        // Docker imajını derle ve Docker Registry'ye push et.
                        def dockerImage = docker.build("y3ko/jenkins:test3")
                        withDockerRegistry(credentialsId: "dockerhub", url: "") {
                            dockerImage.push()
                        }
                    }
                }
            }
        }

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
                        sh "ssh -tt nfs@192.168.1.119 '${remoteCommands}'"
                    }
                }
            }
        }
    }
}
