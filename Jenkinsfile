#!groovy

pipeline {
    agent none

    stages {
        stage('Build') {
            agent { label 'dockerd' }
            steps {
                dockerBuildTagPush()
            }
        }
    }
}
