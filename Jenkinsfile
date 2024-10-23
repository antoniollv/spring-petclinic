#!/usr/bin/env groovy

pipeline {
    agent {
        label 'default'
    }
    stages {
        stage('Run maven') {
            steps {
                container('maven') {
                    sh 'mvn -version'
                }
            }
        }
    }
}
