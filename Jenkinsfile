#!/usr/bin/env groovy

pipeline {
    agent {
        label 'pod-default'
    }
    stages {
        stage('Build') {
            container ('maven') {
                steps {
                    println '01# Stage - Build'
                    println '(develop y main):  Build a jar file.'
                    sh '''
                        java -version
                        mvn -version
                        pwd
                        ls -la
                        ./mvnw package
                        ls -la
                    '''
                }
            }
        }
        stage('Unit Tests') {
            container ('maven') {
                steps {
                    println '04# Stage - Unit Tests'
                    println '(develop y main): Launch unit tests.'
                    sh '''
                        java -version
                        mvn -version
                        pwd
                        ls -la
                        mvn test
                        ls -la
                    '''
                }
            }
        }
    }
}
