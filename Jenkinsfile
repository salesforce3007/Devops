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
           mail body: "Hey this is a test", subject: "DecDemo", to: "ishasingh7003@gmail.com"
           }
}
}
}