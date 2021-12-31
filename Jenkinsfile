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
     stage('Deploy to Production') {
      steps{
        script {
          docker.withServer('${production_server_ip}', 'webserver_server_login-certs') {
            docker.image('$registry:$BUILD_NUMBER').withRun('-p 3000:8080') {
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
