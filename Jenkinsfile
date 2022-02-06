pipeline {
  agent any
  stages {
    stage("sonar quality check") {
      agent {
        docker {
          image 'openjdk:11'
          args '-u root:root'
        }
      }
      steps {
        script {
          withSonarQubeEnv(credentialsId: 'sonar-token') {
            sh 'chmod +x gradlew'
            sh './gradlew sonarqube'
          }
          timeout(time: 5, unit: 'MINUTES') {
            waitForQualityGate abortPipeline: true
          }
        }
      }
    }        
  }
}

