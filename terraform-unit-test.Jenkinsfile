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
    AWS_ACCESS_KEY_ID     = credentials('aws.id')
    AWS_SECRET_ACCESS_KEY = credentials('aws.key')
    KUBECONFIG            = credentials('kubeconfig')
    BACKEND               = credentials('backend.hcl')
    TF_VAR_env            = UUID.randomUUID().toString()
    TF_IN_AUTOMATION      = true
    TF_INPUT              = false
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
          sshagent (credentials: ['id_rsa']) {
            sh 'mkdir -p ~/.ssh && chmod 700 ~/.ssh'
            sh 'ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts'
            sh 'for i in 1 2 3 4 5; do ssh-keyscan -t rsa bitbucket.com >> ~/.ssh/known_hosts && break; done'
            sh 'cp ${BACKEND} backend.tf'
            sh 'terraform init -backend-config key=${JOB_NAME}'
          }
        }
      }
    }
    stage('Apply') {
      steps {
        container('this') {
          sh 'terraform apply -auto-approve test'
        }
      }
    }
    stage('Apply Idempotency Check') {
      steps {
        container('this') {
          sh 'terraform plan -detailed-exitcode test'
        }
      }
    }
    stage('Destroy') {
      steps {
        container('this') {
          sh 'terraform destroy -auto-approve test'
        }
      }
    }
    stage('Destroy Idempotency Check') {
      steps {
        container('this') {
          sh 'terraform plan -detailed-exitcode -destroy test'
        }
      }
    }
  }
}
