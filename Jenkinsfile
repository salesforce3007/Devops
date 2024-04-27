def sfdxcli
def toValidate = false;
def toDeploy=false;

pipeline {
    agent any

    stages {
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
                   withCredentials([file(credentialsId: "${SF_CRED_ID}", variable: 'CERT_KEY_FILE')]) {
                        def status = bat "${sfdxcli}/sf org login jwt --client-id ${SF_CLIENT_ID} --jwt-key-file %CERT_KEY_FILE% --username  ${SF_USER_NAME} --alias ${SF_ALIAS} --instance-url ${SF_INSTANCE_URL} "
                   }
          echo env.GIT_BRANCH
           if(env.GIT_BRANCH=~'feature/*'){
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
                echo "validating ....."
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
