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
    KNOWN_HOSTS           = credentials('known_hosts')
    TF_VAR_env            = "${env.GIT_URL.replaceAll(".*env-", "").replace(".git", "")}"
    TF_IN_AUTOMATION      = true
    TF_INPUT              = false
  }

  stages {
    stage('Initialize') {
      steps {
        container('this') {
          sshagent (credentials: ['id_rsa']) {
            sh '''
              # configure ssh for downloading private modules
              su -c "mkdir -p /root/.ssh" && su -c "chmod 700 /root/.ssh"
              su -c "cp ${KNOWN_HOSTS} /root/.ssh/known_hosts" && su -c "chmod 644 /root/.ssh/known_hosts"

              # init + backend config
              terraform init \\
                -backend-config "bucket=S3_BUCKET" \\
                -backend-config "region=us-east-1" \\
                -backend-config "key=${TF_VAR_env}.tfstate" \\
                -backend-config "encrypt=true"
            '''
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
