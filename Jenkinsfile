pipeline {
agent any
stages{
stage('test'){
   steps{
         echo "test"
}
}
stage('Email build status'){
     steps{
           mail bcc: '', body: 'Hey this is a test', cc: '', from: '', replyTo: '', subject: 'DecDemo', to: 'ishasingh7003@gmail.com'
           }
}
}
}