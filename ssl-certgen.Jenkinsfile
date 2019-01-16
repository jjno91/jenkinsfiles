pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      containerTemplate {
        name 'this'
        image 'frapsoft/openssl'
        ttyEnabled true
        command 'cat'
      }
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '30'))
  }

  stages {
    stage('Generate Cert') {
      steps {
        container('this') {
          sh '''
            openssl req \
              -new \
              -newkey rsa:2048 \
              -days 9999 \
              -nodes \
              -x509 \
              -subj "/C=US/O=YourCompany/CN=yourcompany.saml.test" \
              -keyout ssl.key \
              -out ssl.cert
          '''
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'ssl.*', fingerprint: true, allowEmptyArchive: true
    }
  }
}
