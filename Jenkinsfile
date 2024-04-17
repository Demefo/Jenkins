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
        DOCKER_PASS = credentials('dockerhub') // Assuming 'dockerhub' is the ID of your Jenkins credentials
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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

        stage("Build & Push Docker Image") {
    steps {
        script {
            docker.withRegistry('https://index.docker.io/v1/', DOCKER_USER, DOCKER_PASS) {
                def dockerImage = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
                dockerImage.push()
                dockerImage.push('latest')
            }
        }
    }
    }
    }
    }
}
