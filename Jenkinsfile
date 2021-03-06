pipeline {
  agent {
    kubernetes {
      cloud 'kubernetes'
      inheritFrom 'jenkins-slave'
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
  dnsPolicy: ClusterFirstWithHostNet
  hostNetwork: true
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
        git changelog: false, credentialsId: 'kimtaogithub', poll: false, url: 'https://github.com/deculare/cicd.git'
        container('docker') {
          script {
            docker.withRegistry('', '556ddb0a-d1f8-4ea1-8850-20c27bb805c5') {
                def image = docker.build('kimtao/project:hellowordcicd')
                image.push() 
            }
          }
        }
      }
    }
    stage('Deploy to K8S') {
      steps {
      /*git url: 'https://github.com/deculare/cicd.git'*/
      container('docker') {kubernetesDeploy configs: 'deploy.yaml', dockerCredentials: [[credentialsId: '556ddb0a-d1f8-4ea1-8850-20c27bb805c5']], enableConfigSubstitution: false, kubeConfig: [path: ''], kubeconfigId: 'kubernetes-home-prod', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
        }
      }
    }
  }
}