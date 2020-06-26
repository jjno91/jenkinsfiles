pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      containerTemplate {
        name 'this'
        image 'hasicorp/terraform:latest'
        ttyEnabled true
        command 'cat'
      }
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '30'))
  }

  environment {
    TF_IN_AUTOMATION = true
    TF_INPUT         = false
  }

  stages {
    stage('Validate') {
      steps {
        container('this') {
          sh 'terraform validate'
        }
      }
    }
    stage('Soft Lint') {
      steps {
        container('this') {
          sh 'terraform fmt -recursive'
        }
      }
    }
    stage('Initialize') {
      steps {
        container('this') {
          sh 'terraform init'
        }
      }
    }
    stage('Resource Check') {
      steps {
        container('this') {
          sh 'terraform plan -detailed-exitcode'
        }
      }
    }
  }
}
