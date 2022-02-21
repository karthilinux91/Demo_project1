pipeline {
  environment {
    registry = "karthilinux91/simple_webpage"
    registryCredential = 'Dockerhub@2022'
    dockerImage = ''
  }
  agent { node { label 'docker_node' }
    
  stages {
    stage('Cloning Git') {
      steps {
         git 'https://github.com/karthilinux91/Demo_project1.git'   
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('Push image to Container Registry') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }

}
