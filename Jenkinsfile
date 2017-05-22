pipeline {
  agent any
  stages {
    stage('Initiallize') {
      steps {
        echo 'Jenkins Pipeline'
        sh '''export PATH=$PATH:$GRADLE_HOME
echo $PATH'''
      }
    }
    stage('Build') {
      steps {
        sh './gradlew clean build'
      }
    }
    stage('Test') {
      steps {
        junit 'build/test-results/test/*.xml'
      }
    }
    stage('Quality Check') {
      steps {
        script {
          withSonarQubeEnv('SonarQube') {
            sh './gradlew --info sonarqube'
          }
        }
        
      }
    }
    stage('Quality Gate') {
      steps {
        script {
          timeout(time: 1, unit: 'HOURS') {
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
              error "Pipeline aborted due to quality gate failure: ${qg.status}"
            }else{
              echo "Quality Gate status : ${qg.status}"
            }
          }
        }
        
      }
    }
    stage('Ask Approval') {
      steps {
        slackSend(message: 'slackSend "Ready for Deployment ${env.JOB_NAME} ${env.BUILD_NUMBER} (<${env.BUILD_URL}|Open>)"')
        input(message: 'Can I deploy?', ok: 'Go Ahead', id: '_ready')
      }
    }
    stage('Deploy') {
      steps {
        echo 'Deployed'
      }
    }
  }
}