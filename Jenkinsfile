def sfdxcli
def orgname = 'my-sc-org'
def isPREvent = false
def isMergeEvent = false;
def instance
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

        stage('identify event') {
            steps {
                script {
                    if(env.CHANGE_ID != null) {
                        echo "this is pull request event"
                        env.isPREvent = true
                    } else if(env.GIT_BRANCH == 'main') {
                        echo "this is merge event"
                        env.isMergeEvent = true
                    }
                }
            }

        }

       
        
        stage('Validate') {
            when {
                expression {
                    return env.isPREvent
                }
            }
    
            steps {
                echo "current branch name: ${BRANCH_NAME}"
                echo "CHANGE BRANCH NAME:: ${CHANGE_BRANCH}"
                echo "CHANGE TARGET NAME:: ${CHANGE_TARGET}"
                echo "current commit: ${GIT_COMMIT}"
                bat "${sfdxcli}/sf --version"
                echo "validating current branch"
                echo "instance name: ${env.instance}"
                bat "${sfdxcli}/sf project deploy start -o ${env.instance} --dry-run --json -d force-app/main/default --ignore-conflicts"
            }
        }



        stage ('deployment') {
            when {
                expression {
                    return env.isMergeEvent
                }
            }
            steps {
                echo "deploy event captured, deploying..."
                echo "instance name: ${env.instance}"
                bat "${sfdxcli}/sf project deploy start -o ${env.SF_USERNAME} --dry-run --json -d force-app/main/default --ignore-conflicts"
            }
        }
    }
    

}
