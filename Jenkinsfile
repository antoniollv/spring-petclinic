#!/usr/bin/env groovy

pipeline {
    agent {
        label 'pod-default'
    }
    stages {
        stage('Build') {
            when {
                branch 'main'
                //branch 'develop'
            }
            steps {
                container('maven') {
                    println '01# Stage - Build'
                    println '(develop y main):  Build a jar file.'
                    sh '''
                        java -version
                        mvn -version
                        pwd
                        ./mvnw package -Dmaven.test.skip=true
                    '''
                }
            }
        }
        stage('Unit Tests') {
            when {
                branch 'develop'
            }
            steps {
                container('maven') {
                    println '04# Stage - Unit Tests'
                    println '(develop y main): Launch unit tests.'
                    sh '''
                        java -version
                        mvn -version
                        pwd
                        mvn clean test
                    '''
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
    }
}
