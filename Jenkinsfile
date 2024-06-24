pipeline {
    agent {
        label 'AGENT-1'
    }
    options {
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
        ansiColor('xterm')
    }
    parameters {
        booleanParam(name: 'deploy', defaultValue: false, description: 'Toggle this value')
    }
    environment {
        def appVersion = ''
    }
    stages {
        stage('read the version') {
            steps {
                script{
                    def packageJson = readJson file: 'package.json'
                    appVersion = pakageJson.version
                    echo "application version: $appVersion" 
                }
            }
        }
        stage('Install the Dependencies') {
                steps {
                sh """ 
                npm install
                ls -ltr
                echo "application version: $appVersion"
                """
            }
        }
        stage('Build') {
        steps {
                sh """ 
                zip -q -r backend-${appVersion}.zip * -x Jenkinsfile -x backend-${appVersion}.zip
                ls -ltr
                """
            }
        }
        stage('Destroy') {
             when {
                expression{
                    params.action == 'Destroy'
                }
            } 
            
            steps {
                sh """ 
                cd 01-vpc
                terraform destroy -auto-approve
                """
            }
        }
    }
    post { 
        always { 
            echo 'I will always say Hello again!'
            deleteDir()
        }
        success { 
            echo 'I will run when pipeline is success'
             
        }
        failure { 
            echo 'I will run when pipeline is failure'
        }
    }
}
