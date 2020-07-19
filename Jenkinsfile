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
        sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
       
    }
   }
  stage('Results') {
   steps{
    junit '**/target/surefire-reports/TEST-*.xml'
    archive 'target/*.jar' 
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
