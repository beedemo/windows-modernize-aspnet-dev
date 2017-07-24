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
        stage('Deploy ASP.NET App') {
            agent { label 'windows-docker-static' }
            steps {
                dir('\\v1-src') {
                    powershell 'docker-compose up -d'
                    powershell 'docker container ls'
                }
            }
        }
        stage('View App') {
            agent none
            steps {
                input(message: "Review ASP.NET app and test Sign Up at http://52.10.196.176?", ok: "Yes") 
            }
        }
        stage('Verify App Sign Up') {
            agent { label 'windows-docker-static' }
            steps {
                powershell "docker container exec v1src_product-launch-db_1 powershell -Command \"Invoke-SqlCmd -Query 'SELECT * FROM Prospects' -Database ProductLaunch\""
            }
        }
    }
    post {
        always {
            node('windows-docker-static') {
                dir('\\v1-src') {
                    powershell 'docker-compose down'
                }
            }
        }
    }
}