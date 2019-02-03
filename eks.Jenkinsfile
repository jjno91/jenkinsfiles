pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      containerTemplate {
        name 'this'
        image 'jjno91/terraform-eks:latest'
        ttyEnabled true
        command 'cat'
      }
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '30'))
  }

  environment {
    AWS_ACCESS_KEY_ID = credentials('aws.id')
    AWS_SECRET_ACCESS_KEY = credentials('aws.key')
  }

  parameters {
    string(name: 'COMMAND', defaultValue: 'kubectl get all --all-namespaces')
  }

  stages {
    stage('Run') {
      steps {
        container('this') {

          // https://docs.aws.amazon.com/eks/latest/userguide/create-kubeconfig.html
          configFileProvider([configFile(fileId: 'kubeconfig', variable: 'KUBECONFIG')]) {
            sh '${COMMAND}'
          }
        }
      }
    }
  }
}
