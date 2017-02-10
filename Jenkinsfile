// https://jenkins.io/doc/book/pipeline/syntax/
pipeline {
  agent {
    docker 'maven:3.3.3'
  }

  stages {
    stage('Build') {
      steps {
        checkout scm

        configFileProvider([configFile(fileId: 'maven-settings', variable: 'MAVEN_SETTINGS')]) {
          sh "mvn -V -s $MAVEN_SETTINGS -B -Dmaven.test.failure.ignore clean install"
        }
      }
    }

    stage('Archive') {
      steps {
        junit '**/target/surefire-reports/TEST-*.xml'
        archive 'target/*.jar'
      }
    }
  }

  post {
    always {
      deleteDir()
    }
  }
}
