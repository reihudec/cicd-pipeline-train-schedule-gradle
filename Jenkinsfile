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
                    docker.withServer('$production_server_ip', 'webserver_server_login') {
                        docker.image('$registry:$BUILD_NUMBER').withRun('-p 3000:8080') {
                            sh 'node --version'
                        }
                    }
                }
            } 
        }
        
         stage('DeployToProduction') {
            when {
                branch 'master'
            }
            steps {
                input 'Deploy to Production?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver_server_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    script {
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$production_server_ip \"docker pull reihudec/train-schedule:${env.BUILD_NUMBER}\""
                        try {
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$production_server_ip \"docker stop train-schedule\""
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$production_server_ip \"docker rm train-schedule\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$production_server_ip \"docker run --restart always --name train-schedule -p 3000:8080 -d reihudec/train-schedule:${env.BUILD_NUMBER}\""
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
