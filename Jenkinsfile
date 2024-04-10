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

       stage("SonarQube Analysis"){
           steps {
	           script {
		        withSonarQubeEnv(credentialsId: 'sonarqube') { 
                        sh "mvn sonar:sonar"
		        }
	           }	
           }
       }

       stage("Quality Gate"){
           steps {
               script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'sonarqube'
                }	
            }

        }
   }
}

