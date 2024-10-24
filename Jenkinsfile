#!/usr/bin/env groovy
pipeline {
    agent {
        kubernetes {
            yaml '''
            apiVersion: v1
            kind: Pod
            metadata:
              labels:
                some-label: docker-pod
            spec:
              containers:
              - name: docker
                image: jenkins/jnlp-agent-docker
                securityContext:
                  privileged: true
                volumeMounts:
                - name: docker-socket
                  mountPath: /var/run/docker.sock
              volumes:
              - name: docker-socket
                hostPath:
                  path: /var/run/docker.sock
            '''
        }
    }
    stages {
        stage('Construir imagen Docker') {
            steps {
                container('docker') {
                    sh '''
                        docker image ls
                        ./mvnw spring-boot:build-image
                        docker image ls
                    '''
                }
            }
        }
    }
    post {
        success {
            echo 'Pipeline completado exitosamente'
        }
        failure {
            echo 'Pipeline fallido'
        }
    }
}
