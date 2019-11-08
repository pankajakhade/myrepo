pipeline {
  agent any
  stages {
    stage('Checkout Source') {
      steps {
        git 'https://github.com/deathstrock/myrepo.git'
      }
    }

    stage('Build image') {
      steps {
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }

      }
    }

    stage('Push Image') {
      steps {
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }
        }

      }
    }

    stage('Deploy App') {
      steps {
        script {
          withKubeConfig([credentialsId: 'mykubeconfig', serverUrl: '35.223.235.17']) 
              sh 'kubectl apply -f myweb.yaml'

          kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
        }

      }
    }

  }
  environment {
    registry = 'deathstrock47/newrepo'
    registryCredential = 'dockerhub'
    dockerImage = ''
  }
}
