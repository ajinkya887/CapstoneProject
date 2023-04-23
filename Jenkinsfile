pipeline {
    agent any

     environment{
       registryCredential = 'ecr:us-east-1:ajinkyas'
       appRegistry = "371769801655.dkr.ecr.us-east-1.amazonaws.com/ajinkyas"
       capstoneRegistry = "https://371769801655.dkr.ecr.us-east-1.amazonaws.com/"
       cluster = "ajinkyas"
        service = "ajinkya_s"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/ajinkya887/CapstoneProject.git'
            }
        }

       stage("Docker Build Image"){
           steps{
             script{
                dockerImage = docker.build(appRegistry+ ":$BUILD_NUMBER",".")
             }
           }
      }

       stage('Test Website') {
            steps {
                // Run tests on the website
                sh 'echo "Running tests on the website"'
            }
       }

       stage("Upload App Image"){
         steps{
            script{
                docker.withRegistry(capstoneRegistry, registryCredential){
                    dockerImage.push("$BUILD_NUMBER")
                    dockerImage.push('latest')
                }
            }
         }
      }

      stage('Deploy to ecs'){
         when {
                branch "master"
         }
         steps{
            withAWS(credentials: 'ajinkyas', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
