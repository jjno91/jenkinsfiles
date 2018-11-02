pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      serviceAccount 'jenkins'
      containerTemplate {
        name 'kubectl'
        image 'roffe/kubectl:latest'
        ttyEnabled true
        command 'cat'
      }
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '30'))
  }

  stages {
    stage('Kubectl Dry-Run') {
      steps {
        container('kubectl') {
          sh 'kubectl apply --dry-run=true -f kubernetes'
        }
      }
    }
    stage('Kubectl Apply') {
      when {
        expression { env.BRANCH_NAME == 'master' }
      }
      steps {
        container('kubectl') {
          timeout(time: 5, unit: 'MINUTES') {
            input message: 'Continuing may change or destroy existing infrastructure, be sure to review the dry-run stage before continuing'
          }
          sh 'kubectl apply -f kubernetes'
        }
      }
    }
  }
}
