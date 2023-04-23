pipeline {

    agent any

     environment{
       registryCredential = 'ecr:ap-south-1:Rushikesh'
       appRegistry = "831258972936.dkr.ecr.ap-south-1.amazonaws.com/capstone_project"
       capstoneRegistry = "https://831258972936.dkr.ecr.ap-south-1.amazonaws.com"
       cluster = "Capstone_Project"
        service = "capstone"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/Rushi543/website/'
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
            withAWS(credentials: 'Rushikesh', region: 'us-east-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}