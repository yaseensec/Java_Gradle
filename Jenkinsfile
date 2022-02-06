pipeline {
  agent any
  environment{
    VERSION = "${env.BUILD_ID}"
  }
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
          /* timeout(time: 1, unit: 'HOURS') { */
          /*   def qg = waitForQualityGate() */
          /*   if (qg.status != 'OK') { */
          /*         error "Pipeline aborted due to quality gate failure: ${qg.status}" */
          /*   } */
          /* } */
        }
      }
    }        
    stage {
      steps {
        script {
          withCredentials([string(credentialsId: 'docker-nexus-pass', variable: 'nexus-pass')]) {
            sh '''
              docker build -t 192.168.0.40:8083/springapp:${VERSION} .
              docker login -u admin -p $nexus-pass 192.168.0.40:8083
              docker push 192.168.0.40:8083/script:${VERSION}
              docker rmi 192.168.0.40:8083/script:${VERSION}
            '''
          }
        }
      }
    }
  }
}

