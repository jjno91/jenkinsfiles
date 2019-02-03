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
    password(name: 'USER', defaultValue: 'SECRET', description: 'Username for the database you are connecting to')
    password(name: 'PASSWORD', defaultValue: 'SECRET', description: 'Password for the database you are connecting to')

    // Bonus: instead of loading scripts into the repository with this Jenkinsfile you could implement a file
    // parameter as shown below to dynamically load scripts that may be stored in other repositories.
    //
    // https://jenkins.io/doc/book/pipeline/syntax/#parameters
    // file(name: "FILE", description: "Choose a file to upload")
  }

  stages {
    stage('this') {
      steps {
        container('this') {
          sh 'mysql -h ${TARGET_HOST} -u ${USER} --password=${PASSWORD} < some-script.sql'
        }
      }
    }
  }
}
