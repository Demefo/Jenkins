pipeline {
    agent any
    tools {
        jdk 'java17'
        maven 'maven3'
    }
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "rudiori"
        DOCKER_PASS = ''
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
        JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
                git branch: 'main', credentialsId: 'personal-git', url: 'https://github.com/Demefo/Jenkins'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }

        stage("SonarQube Analysis") {
            steps {
                withSonarQubeEnv(credentialsId: 'sonarqube', installationName: 'sonarqube server') {
                    sh "mvn sonar:sonar"
                }
            }
        }

        stage("Set Docker Credentials") {
        steps {
            script {
                DOCKER_PASS = credentials('dockerhub')
            }
        }
    }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('','dockerhub') {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('','dockerhub') {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }

       }

    //    stage("Trivy Scan") {
    //        steps {
    //            script {
	//             sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image rudiori/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
    //            }
    //        }
    //    }

       stage ('Cleanup Artifacts') {
           steps {
               script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
               }
          }
       }

       stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v -k --user rudi:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'https://6c5a-129-0-60-24.ngrok-free.app:8080/job/register-app-cd/buildWithParameters?token=gitops-token'"
                }
            }
       }
    }
}    
