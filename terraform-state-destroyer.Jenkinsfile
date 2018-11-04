pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      containerTemplate {
        name 'terraform'
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
    AWS_ACCESS_KEY_ID = credentials('aws.id')
    AWS_SECRET_ACCESS_KEY = credentials('aws.key')
  }

  parameters {
    string(name: 'TF_VAR_region', defaultValue: 'us-east-1', description: 'This is the region the infra in the state file is deployed to')
    string(name: 'STATE_FILE_NAME', defaultValue: 'directory/file_name.tfstate')
    string(name: 'S3_BUCKET', defaultValue: 'YOUR_STATE_BUCKET')
    string(name: 'S3_REGION', defaultValue: 'us-east-1')
    choice(name: 'S3_BUCKET_ENCRYPTION', choices: ['true', 'false'])
  }

  stages {
    stage('Initialize') {
      steps {
        container('terraform') {
          sh '''
            terraform init \\
              -backend-config "key=${STATE_FILE_NAME}" \\
              -backend-config "bucket=${S3_BUCKET}" \\
              -backend-config "region=${S3_REGION}" \\
              -backend-config "encrypt=${S3_BUCKET_ENCRYPTION}"
          '''
        }
      }
    }
    stage('Plan') {
      steps {
        container('terraform') {
          sh 'terraform plan -destroy'
        }
      }
    }
    stage('Destroy') {
      steps {
        container('terraform') {
          timeout(time: 5, unit: 'MINUTES') {
            input message: 'Continuing will destroy all existing infrastructure in this state file'
          }
          sh 'terraform destroy -auto-approve'
        }
      }
    }
  }
}
