def sfdxcli
def toValidate = false;
def toDeploy=false;
def instance

pipeline {
    agent any

    stages {
        stage('print env'){
          steps {
                bat 'set'
            }
        }
        stage('setup') {
            steps {
                script {
                    sfdxcli = tool 'sfdx-cli'
                }
            }
        }


       stage('identify event'){
           steps{
               script{
                   withCredentials([file(credentialsId: '684cc70c-9dde-4b78-b5f7-05673548e4c3', variable: 'CERT_KEY_FILE')]) {
                        def status = bat "${sfdxcli}/sf org login jwt --client-id 3MVG9uk_cDhyHiA5ob_g8cpLUOdlbPLt9hMkMECaqIbSXZgEWaGWNkqd1Df9jJBrtF8rYCdcHbYtQXyss0X9b --jwt-key-file %CERT_KEY_FILE% --username  ishasingh7003@brave-unicorn-vcg87b.com --alias qa --instance-url https://login.salesforce.com "
                   }
          echo env.GIT_BRANCH
           if(env.GIT_BRANCH=~'origin/feature/*'){
               echo 'inside featur'
               env.toValidate=true
           }
           else if(env.GIT_BRANCH=='origin/main'){
               echo 'inside main'
               env.toDeploy=true
           }
           }
           }
       }
        
        stage('Validate') {
            when {
                expression {
                    return env.toValidate
                }
            }
    
            steps {
                bat "${sfdxcli}/sf project deploy start -o qa --dry-run --json -d force-app/main/default --ignore-conflicts"
            }
        }



        stage ('deployment') {
            when {
                expression {
                    return env.toDeploy
                }
            }
            steps {
                echo "deploy event captured, deploying..."
                bat "${sfdxcli}/sf project deploy start -o qa --json -d force-app/main/default --ignore-conflicts"
            }
        }
    }
    

}

