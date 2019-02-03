pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      containerTemplate {
        name 'this'
        image 'mysql'
        ttyEnabled true
        command 'cat'
      }
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '30'))
  }

  parameters {
    string(name: 'TARGET_HOST', defaultValue: 'some-rds-instance.sdl2la9sdfjk.us-east-1.rds.amazonaws.com')
    string(name: 'TARGET_DB', defaultValue: 'YourDatabase')
    password(name: 'USER', defaultValue: 'SECRET', description: 'Username for the database you are connecting to')
    password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Password for the database you are connecting to')
  }

  stages {
    stage('this') {
      steps {
        container('this') {
          sh '''
            mysqldump -h ${TARGET_HOST} -u ${USER} --password=${PASSWORD} \
            --no-data \
            --databases \
            --add-drop-database \
            --lock-tables=false \
            --column-statistics=0 \
            ${TARGET_DB} > ${TARGET_DB}.sql
          '''
        }
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: '*.sql', fingerprint: true, allowEmptyArchive: true
    }
  }
}
