#!/usr/bin/env groovy
pipeline {
    agent { label 'pod-default' }
    environment {
        CURRENT_VERSION = ''
    }
    stages {
        stage('Check Environment') {
            steps {
                container('maven') {
                    sh '''
                        java -version
                        mvn -version
                        pwd
                    '''
                    script {
                        env.CURRENT_VERSION = currentVersion()
                    }
                }
                println '01# Stage - Check Environment'
                println '(develop y main):  Checking environment Java & Maven versions.'
                sh 'java -version'
                echo env.CURRENT_VERSION
            }
        }
        stage('Build') {
            when {
                anyOf {
                    branch 'main'
                    //branch 'develop'
                }
            }
            steps {
                container('maven') {
                    println '02# Stage - Build'
                    println '(develop y main):  Build a jar file.'
                    sh './mvnw package -Dmaven.test.skip=true'
                }
            }
        }
        stage('Unit Tests') {
            when {
                anyOf {
                    branch 'main'
                    //branch 'develop'
                }
            }
            steps {
                container('maven') {
                    println '03# Stage - Unit Tests'
                    println '(develop y main): Launch unit tests.'
                    sh '''
                        mvn test
                    '''
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }
        stage('Publish Artifact') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                }
            }
            environment {
                MAVE_REPOSITORY = "${env.GIT_BRANCH == 'main' ? 'maven-release' : (env.GIT_BRANCH == 'development' ? 'maven-snapshots' : '')}"
            }
            steps {
                container('maven') {
                    println '04# Stage - Deploy Artifact'
                    println '(develop y main): Deploy artifact to repository.'
                    echo env.MAVE_REPOSITORY
                    // sh """
                    //     mvn -e deploy:deploy-file \
                    //         -Durl=http://nexus-service:8081/repository/${ env.MAVE_REPOSITORY } \
                    //         -DgroupId=local.moradores \
                    //         -DartifactId=spring-petclinic \
                    //         -Dversion=${ env.CURRENT_VERSION } \
                    //         -Dpackaging=jar \
                    //         -Dfile=target/spring-petclinic-${ env.CURRENT_VERSION }.jar
                    // """
                }
            }
        }
        stage('Build & Publish Container Image') {
            when {
                anyOf {
                    branch 'main'
                    //branch 'develop'
                }
            }
            steps {
                container('kaniko') {
                    println '05# Stage - Build & Publish Container Image'
                    println '(develop y main): Build container image with Kaniko & Publish to container registry.'
                    sh """
                        /kaniko/executor \
                        --context `pwd` \
                        --insecure \
                        --dockerfile Dockerfile \
                        --destination=nexus-service:8082/repository/docker/spring-petclinic:${ env.CURRENT_VERSION} \
                        --destination=nexus-service:8082/repository/docker/spring-petclinic:latest \
                        --build-arg JAR_FILE=spring-petclinic-${ env.CURRENT_VERSION}.jar
                    """
                }
            }
        }
        stage('Deploy petclinic') {
            when {
                anyOf {
                    branch 'main'
                    branch 'develop'
                }
            }
            environment {
                PORT = "${env.GIT_BRANCH == 'origin/main' ? '80' : (env.GIT_BRANCH == 'origin/development' ? '8080' : '')}"
            }

            steps {
                println '06# Stage - Deploy petclinic'
                println '(develop y main): Deploy petclinic app to MicroK8s.'
                echo env.PORT
                // sh '''
                //     curl -LO "https://dl.k8s.io/release/$(curl -L \
                //         -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
                //     chmod +x kubectl
                //     mkdir -p ~/.local/bin
                //     #mv ./kubectl ~/.local/bin/kubectl

                //     ./kubectl version --client

                //     export IP_SERVICIO_NEXUS=$(kubectl get services| grep 'nexus' | awk '{print $3}')

                //     ./kubectl create deployment petclinic \
                //         --image $IP_SERVICIO_NEXUS:8082/repository/docker/spring-petclinic:latest || \
                //         echo 'Deplyment petclinic already exists, creating service...'
                //     ./kubectl expose deployment petclinic --port 8080 --target-port 8080 \
                //         --selector app=petclinic --type ClusterIP --name petclinic

                //     ./kubectl get all
                // '''
            }
        }
        stage('Release Promotion Branch main') {
            when {
                branch 'develop'
            }
            steps {
                println '07# Stage - Release Promotion Branch main'
                println '(develop): Release Promotion Branch main update pom version'
                script {
                    def releaseVersion = env.CURRENT_VERSION.replace('-SNAPSHOT', '')

                    sh "mvn versions:set -DnewVersion=${releaseVersion}"

                    sh 'git add pom.xml'
                    sh "git commit -m 'Release version ${releaseVersion}'"
                    sh 'git push origin master'
                }
            }
        }
        stage('Release Promotion Branch develop') {
            when {
                branch 'main'
            }
            steps {
                println '07# Stage - Release Promotion Branch develop'
                println '(main): Release Promotion Branch develop update pom version'
                script {
                    def (major, minor, patch) = env.CURRENT_VERSION.split('\\.')
                    def newSnapshotVersion = "${major}.${minor}.${patch.toInteger() + 1}-SNAPSHOT"

                    sh "mvn versions:set -DnewVersion=${newSnapshotVersion}"

                    sh 'git add pom.xml'
                    sh "git commit -m 'Release version to ${newSnapshotVersion}'"
                    sh 'git push origin develop'
                }
            }
        }
    }
}

def currentVersion() {
    return sh(script: "mvn help:evaluate -Dexpression=project.version -q -DforceStdout", returnStdout: true).trim()
}
