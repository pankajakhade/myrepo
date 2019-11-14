pipeline {

  environment {
    registry = "deathstrock47/$REPO" //qa
    namespace = "$NAMESPACE" //qa
    port = "$PORT" //32001
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
        //sh """ git checkout $BRANCH """
      }
    }
  
    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
          BUILD = "$registry:$BUILD_NUMBER"
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
    //stage('merge to staging'){
    //  
    //}
    stage('Stagging') {
        steps {
          script {
            sed "s/namespace/c\namespace: $namespace/ myweb.yaml"
            sed "s/nodePort/c\nodePort: $port/ myweb.yaml"
            sed "s/image/c\- image: $BUILD"
            kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
          }
        }
      }

    stage('Deployment Confirmation'){
      input {
            message "Should we continue?"
            ok "Yes, we should."
            submitter "admin"
      }
      steps{
        sh ''' ./patch.sh $VERSION   '''
      }
    }
    //stage('Staging Deployment'){
    //  steps{
    //      script {
    //        //kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
    //      }
    //    }
    //  }
  }
}
