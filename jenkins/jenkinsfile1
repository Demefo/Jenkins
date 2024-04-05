pipeline {
    agent any
    tools {
         jdk 'java17'
         maven 'maven3'
    }
    stages{
        stage("Cleanup Workspace") {
              steps {
              cleanWs()
              }
        }

        stage ("Checkout from SCM"){
              steps {
              git branch: 'main' , credentialsId: 'personal-git' , url: 'https://github.com/Demefo/Jenkins'
              }
        }

        
        stage("Build Application"){
            steps {
                sh "mvn clean package"
            }

       }

       stage("Test Application"){
           steps {
                 sh "mvn test"
           }
       }
   }
}

