pipeline {
 environment {
 registry = "pedromasa/ci"
 registryCredential = 'dockerhub'
 dockerImage = ''
 } 
agent any
 stages {
  stage('Poll') {
   steps{
     checkout scm
    }
   }
  stage('Build & Unit test'){
   steps{
        
        echo 'Hello, Maven'
        sh 'mvn clean package'
           
    }
   }
   stage('Static Code Analysis'){
    steps{
        sh 'mvn sonar:sonar -Dsonar.projectName=ci-project -Dsonar.projectKey=ci-project -Dsonar.projectVersion=$BUILD_NUMBER -Dsonar.host.url=http://192.168.0.114:9000';
     }
    }
   stage ('Integration Test'){
    steps{
        sh 'mvn clean verify -Dsurefire.skip=true';
     }
   }
  
  stage('Building Docker Image') {
   steps{
     script {
       dockerImage = docker.build registry + ":$BUILD_NUMBER"
     }
  }
 }
 stage('Push Image to Docker Hub ') {
  steps{
    script {
      docker.withRegistry( '', registryCredential ) {
      dockerImage.push()
      dockerImage.push('latest')
   }
  }
 }}
  
   }
}
