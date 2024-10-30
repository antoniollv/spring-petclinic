#!/usr/bin/env groovy

pipeline {
    agent {
        label 'pod-default'
    }
    stages {
        stage('Check Environment') {
            steps {
                sh 'java -version'
                container('maven') {
                    sh '''
                        java -version
                        mvn -version
                        pwd
                    '''
                }
            }
        }
        stage('Build') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                }
            }
            steps {
                container('maven') {
                    println '01# Stage - Build'
                    println '(develop y main):  Build a jar file.'
                    sh './mvnw package -Dmaven.test.skip=true'
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
                        mvn test
                    '''
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('Publish Artifact') {
            steps {
                container('maven') {
                    println '03# Stage - Deploy Artifact'
                    println '(develop y main): Deploy artifact to repository.'
                    sh '''
                        mvn -e deploy:deploy-file \
                            -Durl=http://nexus-service:8081/repository/maven-snapshots \
                            -DgroupId=local.moradores \
                            -DartifactId=spring-petclinic \
                            -Dversion=3.3.0-SNAPSHOT \
                            -Dpackaging=jar \
                            -Dfile=target/spring-petclinic-3.3.0-SNAPSHOT.jar
                    '''
                }
            }
        }
        stage('Build & Publish Container Image') {
            steps {
                container('kaniko') {
                    println '04# Stage - Build & Publish Container Image'
                    println '(develop y main): Build container image with Kaniko & Publish to container registry.'
                    sh '''
                        /kaniko/executor \
                        --context `pwd` \
                        --dockerfile Dockerfile \
                        --destination=nexus-service/repository/docker/spring-petclinic:3.3.0-SNAPSHOT \
                        --destination=nexus-service/repository/docker/spring-petclinic:latest \
                        --build-arg JAR_FILE=spring-petclinic-3.3.0-SNAPSHOT.jar
                    '''
                }
            }
        }
    }
}
