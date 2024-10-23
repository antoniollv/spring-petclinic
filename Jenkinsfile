#!/usr/bin/env groovy

pipeline {
    agent {
        kubernetes {
            defaultContainer 'maven'
        }
    }

    stages {
        stage('Run maven') {
            steps {
                sh 'mvn -version'
            }
        }
    }
}
