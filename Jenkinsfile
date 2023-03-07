pipeline {

  environment {
    dockerimagename = "nawochus/nodeapp"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/nawochus/test-nodeapp.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build("nawochus/nodeapp:v${env.BUILD_ID}")
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhublogin'
           }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push()
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        script {
          withKubeCredentials(kubectlCredentials: [[credentialsId: 'kubernetes', serverUrl: 'https://172.16.30.200:6443']]) {
             sh 'kubectl apply -f deploymentservice.yml'
           }
          }
        }
      }
    }

}
