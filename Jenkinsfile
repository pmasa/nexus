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
        sh "mvn package"
       
    }
   }

   stage ('Integration Test'){
    steps{
        sh 'mvn clean verify -Dsurefire.skip=true';
        junit '**/target/surefire-reports/TEST-*.xml'
        archive 'target/*.jar' 
     }
   }
  stage ('Publish'){  
   steps{
    script {
       def server = Artifactory.newServer url: 'http://localhost:8080/artifactory/', username: 'admin', password: 'password'
       def uploadSpec = """{
       "files": [ 
        {
            "pattern": "target/helloworld-app.jar",
            "target": "jcenter/${BUILD_NUMBER}/",
            "props": "Integration-Tested=Yes;Performance-Tested=No"
       } 
      ]
     }"""
    server.upload(uploadSpec) 
    }
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
