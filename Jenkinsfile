pipeline {
    agent any

     environment{
       registryCredential = 'ecr:ap-northeast-1:user'
       appRegistry = "821265209387.dkr.ecr.ap-northeast-1.amazonaws.com/abc"
       capstoneRegistry = "https://821265209387.dkr.ecr.ap-northeast-1.amazonaws.com"
       cluster = "acbd"
        service = "sharma"
   }

    stages {
        stage('Clone Website') {
            steps {
                git url:'https://github.com/iampiyushsharma/website'
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
            withAWS(credentials: 'user', region: 'ap-northeast-1'){
                sh 'aws ecs update-service --cluster ${cluster} --service ${service} --force-new-deployment'
            }
         }
      }

    }
}
