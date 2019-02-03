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

  stages {
    stage('Run') {
      steps {
        container('this') {

          // load known_hosts to prevent the host confirmation prompt
          configFileProvider([configFile(fileId: 'known_hosts', variable: 'KNOWN_HOSTS')]) {
            sh 'su -c "mkdir -p ~/.ssh" && su -c "chmod 700 ~/.ssh"'
            sh 'su -c "cp ${KNOWN_HOSTS} ~/.ssh/known_hosts" && su -c "chmod 644 ~/.ssh/known_hosts"'
          }

          // load private key from Jenkins credentials plugin
          sshagent (credentials: ['id_rsa']) {

            // download module dependencies from a private repo
            // execute ssh commands on a remote server
            // do anything you would do with proper SSH credentials loaded
          }
        }
      }
    }
  }
}
