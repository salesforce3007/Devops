def sfdxcli
def toValidate = false
def toDeploy=false
def isPREvent = false

pipeline {
    agent any

    stages {
        stage('setup') {
            steps {
                script {
                    sfdxcli = tool 'sfdx-cli'
                    writeFile file: 'log.txt', text: 'Temporary log file.'
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
               echo 'inside feature'
               env.toValidate=true
               env.isPREvent = true
           }
           else if(env.GIT_BRANCH=='origin/main'){
               echo 'inside main'
               env.toDeploy=true
           }
           }
           }
       }
       stage('scan code for vulnerabilities') {
            when {
                expression {
                    return env.isPREvent
                }
            }
    
            steps {
                echo "running sf scanner..."
                bat "${sfdxcli}/sf scanner run --target '.\\**\\*.js,.\\**\\*.cls' --severity-threshold 1"
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
                mail body: "validation", subject: "DecDemo", to: "ishasingh7003@gmail.com"
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
              mail body: "deploy", subject: "DecDemo", to: "ishasingh7003@gmail.com"
            }
        }
    }
    post {
        always {
            emailext (
                subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}",
                 body: '''Hello,

                Please find the attached report for the build.

                Thanks,
                Jenkins
                ''',
                attachmentsPattern: 'log.txt',
                recipientProviders:  [requestor()]
            )
            bat 'del  log.txt'
        }
    }

}
