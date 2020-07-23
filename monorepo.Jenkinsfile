pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      yaml """
apiVersion: v1
kind: Pod
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
  containers:
  - name: java
    image: openjdk
    command:
    - cat
    tty: true
  - name: node
    image: node
    command:
    - cat
    tty: true
  - name: mysql
    image: mysql
    tty: true
    env:
    - name: MYSQL_ROOT_PASSWORD
      value: "this"
    - name: MYSQL_DATABASE
      value: "this"
    args:
    - --lower_case_table_names=1
  - name: docker
    image: docker:stable-git
    command:
    - cat
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-socket
  volumes:
  - name: docker-socket
    hostPath:
      path: /var/run/docker.sock
"""
    }
  }

  options {
    buildDiscarder(logRotator(numToKeepStr: '30'))
  }

  stages {
    stage('comp1') {
      when {
        changeset "**/comp1/*.*"
        branch "master"
      }
      steps {
        container('java') {
          dir("comp1") {
            sh 'java --version'
          }
        }
      }
    }
    stage('comp2') {
      when {
        changeset "**/comp2/*.*"
        branch "master"
      }
      steps {
        container('node') {
          dir("comp2") {
            sh 'node --version'
          }
        }
      }
    }
    stage('docker tag') {
      steps {
        container('docker') {
          sh 'echo "loop through components"'
        }
      }
    }
    stage('deploy') {
      when {
        not {
          branch "master"
        }
      }
      steps {
        container('docker') {
          sh 'echo "loop through components"'
        }
      }
    }
  }
}
