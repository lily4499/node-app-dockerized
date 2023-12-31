pipeline {
    agent any 

     options {
        timeout(time: 10, unit: 'MINUTES')
     }
    environment {
    DOCKERHUB_CREDENTIALS = credentials('lily-docker-credentials')
    APP_NAME = "laly9999/node-app-dockerized"
    }
    stages { 
        stage('SCM Checkout') {
            steps{
           git branch: 'main', url: 'https://github.com/lily4499/node-app-dockerized.git'
            }
        }
        // run sonarqube test
        stage('Run Sonarqube') {
            environment {
                scannerHome = tool 'ibt-sonarqube';
            }
            steps {
              withSonarQubeEnv(credentialsId: 'ibt-sonar', installationName: 'IBT sonarqube') {
                sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=may17_cohort"
              }
            }
        }
        stage('Build docker image') {
            steps {  
                sh 'docker build -t $APP_NAME:$BUILD_NUMBER .'
            }
        }
        stage('login to dockerhub') {
            steps{
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Trivy Scan (Aqua)') {
            steps {
                sh 'trivy image $APP_NAME:$BUILD_NUMBER'
            }
       }
        stage('push image') {
            steps{
                sh 'docker push $APP_NAME:$BUILD_NUMBER'
            }
        }
	stage('Trigger ManifestUpdate') {
             steps{
                build job: 'node-app-manifest', parameters: [string(name: 'IMAGE_TAG', value: env.BUILD_NUMBER)]     

            } 
           } 
     }
}

