#!/usr/bin/env groovy

pipeline {
    agent {
        label 'default'
    }
    stages {
        stage('Run maven') {
            steps {
                sh 'mvn -version'
            }
        }
    }
}
