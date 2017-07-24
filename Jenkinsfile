pipeline {
    options { 
        buildDiscarder(logRotator(numToKeepStr: '5')) 
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
                dir('\\v1-src\\docker\\builder') {
                    powershell 'docker image build -t $env:DOCKER_HUB_USER/modernize-aspnet-builder .'
                }
            }
        }
        stage('Build ASP.NET App') {
            agent { label 'windows-docker-static' }
            steps {
                dir('\\v1-src') {
                    powershell 'docker container run --rm -v $pwd\\ProductLaunch:c:\\src -v $pwd\\docker:c:\\out $env:DOCKER_HUB_USER/modernize-aspnet-builder C:\\src\\build.ps1'
                }
            }
        }
        stage('Package ASP.NET App') {
            agent { label 'windows-docker-static' }
            steps {
                dir('\\v1-src\\docker\\web') {
                    powershell 'docker image build -t $env:DOCKER_HUB_USER/modernize-aspnet-web:v1 .'
                }
            }
        }
    }
}