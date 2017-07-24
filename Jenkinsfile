pipeline {
    options { 
        buildDiscarder(logRotator(numToKeepStr: '5')) 
        skipDefaultCheckout() 
        timeout(time: 1, unit: 'HOURS')
    }
    agent none
    environment {
        DOCKER_HUB_USER = 'beedemo'
        DOCKER_CREDENTIAL_ID = 'docker-hub-beedemo'
    }
    stages {
        stage('Create Build Image') {
            agent { label 'windows-docker-static' }
            steps {
                checkout scm
                dir('\\v1-src\\docker\\builder') {
                    powershell 'docker image build -t $env:DOCKER_HUB_USER/modernize-aspnet-builder .'
                }
            }
        }
    }
}