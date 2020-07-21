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
       def server = Artifactory.server url: 'localhost:8080', username: 'admin', password: 'password'
       def uploadSpec = """{
       "files": [ 
        {
            "pattern": "target/hello-0.0.1.war",
            "target": "example-project/${BUILD_NUMBER}/",
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
