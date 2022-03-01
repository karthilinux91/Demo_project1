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
    
  
       stage('prod deployment') {
          steps{              
        sh '/home/centos/linux-amd64/kubectl --kubeconfig /home/centos/linux-amd64/kube.config apply -f k8s/blue.yaml  --namespace blue-green-ns'
                }
                                } 
     stage('stage deployment') {
          steps{              
        sh '/home/centos/linux-amd64/kubectl --kubeconfig /home/centos/linux-amd64/kube.config apply -f k8s/green.yaml  --namespace blue-green-ns'
                }
                                } 
      stage ('WaitForTestingStage') {
            input {
                message "Ready to SWAP Live Listener?"
                ok "Yes, go ahead."
            }
            steps{
                echo "Moving on to perform SWAP ..................."
            }            
        }
    
      stage('switch traffic blue to green') {
          steps{              
        sh '/home/centos/linux-amd64/kubectl --kubeconfig /home/centos/linux-amd64/kube.config apply -f k8s/blue_to_green.yaml  --namespace blue-green-ns'
                }
                                } 
    
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
      }
    }
  }
}
