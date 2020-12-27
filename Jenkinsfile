pipeline {
  agent {
    kubernetes {
      //cloud 'kubernetes'
      label 'mypod'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  name: docker
spec:
  containers:
  - name: docker
    image: docker:1.11
    command: ['cat']
    tty: true
    volumeMounts:
    - name: dockersock
      mountPath: /var/run/docker.sock
  volumes:
  - name: dockersock
    hostPath:
      path: /var/run/docker.sock
"""
    }
  }
  stages {
    stage('Build Docker image') {
      steps {
        git 'https://github.com/deculare/cicd.git'
        container('docker') {
          script {
            def image = docker.build('kimtao/project:hellowordcicd')
            image.inside() {
              sh "whoami"
            }
          }
        }
      }
    }
  }
}