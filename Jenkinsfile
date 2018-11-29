// https://jenkins.io/doc/book/pipeline/syntax/
pipeline {
  agent { label 'ci' }
  
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
        junit '**/target/surefire-reports/TEST-*.xml'
        archiveArtifacts allowEmptyArchive: true, artifacts: 'target/*.jar', onlyIfSuccessful: true
      }
    }
  }
}
