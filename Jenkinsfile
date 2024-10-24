#!/usr/bin/env groovy
pipeline {
    agent {
        kubernetes {
            label 'docker-agent'
            yaml '''
            apiVersion: v1
            kind: Pod
            spec:
              containers:
              - name: docker
                image: docker:20.10.8
                securityContext:
                  privileged: true
                volumeMounts:
                - name: docker-socket
                  mountPath: /var/run/docker.sock
                - name: workspace-volume
                  mountPath: /workspace
              volumes:
              - name: docker-socket
                hostPath:
                  path: /var/run/docker.sock
              - name: workspace-volume
                emptyDir: {}
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
