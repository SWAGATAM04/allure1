pipeline {
    agent {
        kubernetes {
            yaml """
kind: Pod
spec:
  containers:
 - name: maven
    image: maven:3.5.3-jdk-8
    imagePullPolicy: Always
    command: [cat]
    tty: true
"""
        }
    }

    stages {
      stage('Integration Tests') {
        steps {
          container('maven') {
            sh 'mvn test'
          }
        }
        post {
          always {
            script {
              allure([
                includeProperties: false,
                jdk: '',
                properties: [],
                reportBuildPolicy: 'ALWAYS',
                results: [[path: 'target/allure-results']]
              ])
            }
          }
        }
      }
    }
}
