pipeline {
  environment {
    registry = "karthilinux91/simple_webpage"
    registryCredential = 'Docker_ID'
    dockerImage = ''
  }
  agent { node { label 'docker_node'  }}

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
          docker.withRegistry( '',registryCredential) {
            dockerImage.push()
          }
        }
      }
    }
    
    stage('k8s deployment using helm') {
          steps{
              
        sh '''/home/centos/linux-amd64/helm version
        cat ./HelmChart/application/values.yaml |  grep firstservice: | head -n 1
        output=`(cat ./HelmChart/application/values.yaml |  grep firstservice: | head -n 1 | awk -F ":" '{print $2}' | xargs)`
        sed -i  "s@$output@$BUILD_NUMBER@" ./HelmChart/application/values.yaml
        /home/centos/linux-amd64/helm upgrade --install --namespace db-system --timeout 10m --wait jenkinsdep ./HelmChart/application/ -f ./HelmChart/application/values.yaml --kubeconfig /home/centos/linux-amd64/kube.config
        cat ./HelmChart/application/values.yaml  |  grep firstservice: | head -n 1
        '''   
      }
    } 
    
    
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}
