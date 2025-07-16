pipeline {
  agent any

  tools {
    nodejs "NodeJS 23"   // Set this in Jenkins global tools
  }

  environment {
    SONAR_HOST_URL = 'http://localhost:9000' // Change if hosted elsewhere
  }

  stages {
    stage('Install Dependencies') {
      steps {
        sh 'npm install'
      }
    }

    stage('Start App') {
      steps {
        sh 'nohup node app.js &'
        sh 'sleep 10'
      }
    }

    stage('SonarQube Scan') {
      steps {
        withSonarQubeEnv('MySonarQube') {
          sh 'sonar-scanner'
        }
      }
    }

    stage('OWASP ZAP Scan') {
      steps {
        sh '''
          docker run --network host -t owasp/zap2docker-stable zap-baseline.py -t http://localhost:3000 -g gen.conf -r zap-report.html
        '''
      }
    }
  }

  post {
    always {
      archiveArtifacts artifacts: 'zap-report.html', allowEmptyArchive: true
    }
  }
}
