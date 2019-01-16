pipeline {
  agent {
    kubernetes {
      label UUID.randomUUID().toString()
      defaultContainer 'jnlp'
      yaml """
apiVersion: v1
kind: Pod
spec:
  securityContext:
    runAsUser: 0
    fsGroup: 0
  containers:
  - name: this
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

  environment {
    REGISTRY = "YOUR_REGISTRY"
  }

  stages {
    stage('Build') {
      steps {
        container('this') {
          sh 'docker build -t $REGISTRY:latest .'
        }
      }
    }
    stage('Tag') {
      steps {
        container('this') {
          sh 'docker tag $REGISTRY:latest $REGISTRY:$(git rev-parse --short HEAD)'
        }
      }
    }
    stage('Push') {
      steps {
        container('this') {
          sh 'docker push $REGISTRY:latest'
          sh 'docker push $REGISTRY:$(git rev-parse --short HEAD)'
        }
      }
    }
  }
}
