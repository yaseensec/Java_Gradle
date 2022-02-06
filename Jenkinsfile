pipeline {
  agent any
  environment{
    VERSION = "${env.BUILD_ID}"
  }
  stages {
    /* stage("sonar quality check") { */
    /*   agent { */
    /*     docker { */
    /*       image 'openjdk:11' */
    /*       args '-u root:root' */
    /*     } */
    /*   } */
    /*   steps { */
    /*     script { */
    /*       withSonarQubeEnv(credentialsId: 'sonar-token') { */
    /*         sh 'chmod +x gradlew' */
    /*         sh './gradlew sonarqube' */
    /*       } */
    /*       timeout(time: 5, unit: 'MINUTES') { */
    /*         waitForQualityGate abortPipeline: true */
    /*       } */
    /*     } */
    /*   } */
    /* }         */
    /* stage("Docker build and Docker Push to Nexus") { */
    /*   steps { */
    /*     script { */
    /*       withCredentials([string(credentialsId: 'docker-nexus-pass', variable: 'nexus_pass')]) { */
    /*         sh ''' */
    /*           docker build -t 192.168.0.40:8083/springapp:${VERSION} . */
    /*           docker login -u admin -p $nexus_pass 192.168.0.40:8083 */
    /*           docker push 192.168.0.40:8083/springapp:${VERSION} */
    /*           docker rmi 192.168.0.40:8083/springapp:${VERSION} */
    /*         ''' */
    /*       } */
    /*     } */
    /*   } */
    /* } */
    stage('indentifying misconfigs using datree in helm charts'){
      steps{
        script{
          dir('kubernetes/') {
            withEnv(['DATREE_TOKEN=2fy66TZiWcfTTQQhCrbspX']) {
                  sh 'helm datree test myapp/'
            }
          }
        }
      }
    }
    stage("pushing the helm charts to nexus"){
      steps{
        script{
          withCredentials([string(credentialsId: 'docker-nexus-pass', variable: 'nexus_pass')]) {
            dir('kubernetes/') {
               sh '''
                   helmversion=$( helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' ')
                   tar -czvf  myapp-${helmversion}.tgz myapp/
                   curl -u admin:$nexus_pass http://192.168.0.40:8081/repository/helm-nexus/ --upload-file myapp-${helmversion}.tgz -v
              '''
            }
          }
        }
      }
    }
  }
  post {
	  always {
			mail bcc: '', body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}", cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}", to: "yaseensec@outlook.com";  
		}
	}
}

