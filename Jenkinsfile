// https://jenkins.io/doc/book/pipeline/syntax/
pipeline {
  agent {
    kubernetes {
      label 'agent-k8s'
      defaultContainer 'maven'
      yaml """
apiVersion: v1
kind: Pod
metadata:
  labels:
    owner: ci
spec:
  containers:
  - name: maven
    image: maven:3.6.0-jdk-8-alpine
    command:
    - cat
    tty: true
"""
    }
  }
  
  stages {
    stage('Build') {
      steps {
        checkout scm
        container('maven') {
          sh "mvn -V -B -Dmaven.test.failure.ignore clean verify"
        }
      }
    }

    stage('Archive') {
      steps {        
        archiveArtifacts allowEmptyArchive: true, artifacts: 'target/*.jar', onlyIfSuccessful: true
      }
    }
  }
  
  post {
    unsuccessful {
      script {
        def summary = junit 'target/surefire-reports/TEST-*.xml'
        slackSend (
           channel: "@cuong.tran",
           color: 'warning',
           message: """Build failed - <${env.RUN_DISPLAY_URL}|${env.JOB_BASE_NAME} #${env.BUILD_NUMBER}>
           *Test Summary* - ${summary.totalCount}, Failures: ${summary.failCount}, Skipped: ${summary.skipCount}, Passed: ${summary.passCount}"""
        )
      }
    }
    
    fixed {
      slackSend channel: '@cuong.tran', color: 'good',
        message: "Build fixed - <${env.RUN_DISPLAY_URL}|${env.JOB_BASE_NAME} #${env.BUILD_NUMBER}>"
    }
  }
}
