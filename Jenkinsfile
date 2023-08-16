pipeline {

  environment {
    registry = "ssrika043/flask"
    registry_mysql = "ssrika043/mysql"
    dockerImage = ""
  }

  agent any
    stages {
  
    stage('Checkout Source') {
      steps {
        git 'https://github.com/srisrika/Docker.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage('docker login') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'mydockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {    
         sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}" 
        }
    }
    }  
    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('current') {
      steps{
        dir("${env.WORKSPACE}/mysql"){
          sh "pwd"
          }
      }
   }
   stage('Build mysql image') {
     steps{
       sh 'docker build -t "ssrika043/mysql:$BUILD_NUMBER"  "$WORKSPACE"/mysql'
        sh 'docker push "ssrika043/mysql:$BUILD_NUMBER"'
        }
      }
    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "frontend.yaml", kubeconfigId: "kube")
        }
      }
    }

  }
}
