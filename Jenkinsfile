pipeline {
  agent any

  tools {
    nodejs 'NodeJS 23'  // Make sure it's configured in Jenkins > Global Tool Config
  }

  environment {
    SCANNER_HOME = tool 'sonar-scanner'       // Jenkins sonar-scanner name
    SONAR_HOST_URL = 'http://localhost:9000'  // Update if hosted externally
  }

  stages {
    stage('Checkout Code') {
      steps {
        git branch: 'main',
            url: 'https://github.com/AAKASHDEEP786/Security-CI.git'
      }
    }

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
        withSonarQubeEnv('sonar') {
          sh '''$SCANNER_HOME/bin/sonar-scanner \
            -Dsonar.projectKey=security-ci-demo \
            -Dsonar.projectName="Security CI Demo" \
            -Dsonar.sources=. \
            -Dsonar.host.url=$SONAR_HOST_URL \
            -Dsonar.sourceEncoding=UTF-8'''
        }
      }
    }

    stage('Quality Gate Check') {
      steps {
        timeout(time: 1, unit: 'HOURS') {
          waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
        }
      }
    }
  }

  post {
    always {
      echo 'Pipeline finished. Check SonarQube for analysis results.'
    }
  }
}
