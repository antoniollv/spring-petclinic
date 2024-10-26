#!/usr/bin/env groovy

pipeline {
    agent {
        label 'pod-default'
    }
    stages {
        stage('Build') {
            steps {
                println '01# Stage - Build'
                println '(develop y main):  Build a jar file.'
                sh '''
                    pwd
                    mvn -version
                    ./mvnw package
                    ls -la
                '''
            }
        }
        stage('Unit Tests') {
            steps {
                println '04# Stage - Unit Tests'
                println '(develop y main): Launch unit tests.'
                sh '''
                    pwd
                    mvn test
                    ls -la
                '''
            }
        }
    }
}
