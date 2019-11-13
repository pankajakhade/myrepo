pipeline {

  environment {
    registry = "deathstrock47/newrepo"
    dockerImage = ""
    registryCredential = "dockerhub"
  }

  agent any

  stages {
  //  stage ('confirmation') {
  //    steps{
  //    input('do you wish to continue')
  //    }
  //  }
    stage('Checkout Source') {
      steps {
        git 'https://github.com/deathstrock/myrepo.git'
      }
    }
  
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
  
    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "" , registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }
  
    stage('Stagging') {
        steps {
          script {
            kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
          }
        }
      }
    //stage('Changing tag to staging'){
    //  steps{
    //      
    //  }
    //}
    stage('Deployment Confirmation'){
      input{
          message "enter stable image version"
          ok "continue..."
          parameters {
              string(name: 'VERSION', defaultValue: '', description: 'Enter image version for production')
          }
        }
      steps{
        sh ''' kubectl patch deployment myweb -p   '{"spec":{"template":{"spec":{"image":$registry:$VERSION}}}}' '''
      }
    }
  //  stage('Staging Deployment'){
  //    steps{
  //        //withCredentials([kubeconfigContent(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG_CONTENT')]) {
  //        //withCredentials([kubeconfigContent(credentialsId: 'mykubeconfig', variable: 'KUBECONFIG_CONTENT')]) {
  //        //sh '''kubectl apply -f myweb.yaml'''
  //        script {
  //          //kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
  //        }
  //      }
  //    }
  //  //stage('Production Deployment'){
    //
    //
    //}
  }

}
