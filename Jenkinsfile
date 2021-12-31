pipeline {
  environment {
    registry = "reihudec/train-schedule"
    registryCredential = 'dockerhub'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Building Docker Image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push Docker Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused Docker Image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
      
  }
}
