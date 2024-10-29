#!/usr/bin/env groovy

pipeline {
    agent {
        label 'pod-default'
    }
    stages {
        stage('Build') {
            when {
                branch 'main'
                branch 'develop'
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
                    println '02# Stage - Unit Tests'
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
        stage('Deploy Artifact') {
            steps {
                container('maven') {
                    println '03# Stage - Deploy Artifact'
                    println '(develop y main): Deploy artifact to repository.'
                    sh '''
                        mvn deploy \
                        -DgroupId=com.ejemplo \
                        -DartifactId=spring-petclinic \
                        -Dversion=3.3.0-SNAPSHOT \
                        -Dpackaging=jar \
                        -Dfile=target/spring-petclinic-3.3.0-SNAPSHOT.jar \
                        -DrepositoryId=maven-snapshots \
                        -Durl=https://nexus:8081/repository/maven-snapshots \
                        -Dusername=anonymous \
                        -Dpassword=''
                    '''
                }
            }
        }
    }
}
