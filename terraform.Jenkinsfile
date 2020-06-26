pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      containerTemplate {
        name 'this'
        image 'hashicorp/terraform:latest'
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
    TF_VAR_env            = "${env.GIT_URL.replaceAll(".*env-", "").replace(".git", "")}"
    TF_IN_AUTOMATION      = true
    TF_INPUT              = false
  }

  stages {
    stage('Initialize') {
      steps {
        container('this') {
          sshagent (credentials: ['id_rsa']) {
            sh 'mkdir -p ~/.ssh && chmod 700 ~/.ssh'
            sh 'ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts'
            sh 'for i in 1 2 3 4 5; do ssh-keyscan -t rsa bitbucket.com >> ~/.ssh/known_hosts && break; done'
            sh 'cp ${BACKEND} backend.tf'
            sh 'terraform init -backend-config key=${TF_VAR_env}'
          }
        }
      }
    }
    stage('Plan') {
      steps {
        container('this') {
          sh 'terraform plan -out .tfplan'
        }
      }
    }
    stage('Apply') {
      when {
        beforeInput true
        branch "master"
      }
      input {
        message "Continuing may change or destroy existing infrastructure, be sure to review the plan stage before continuing"
      }
      steps {
        container('this') {
          sh 'terraform apply -auto-approve .tfplan'
        }
      }
    }
  }
}
