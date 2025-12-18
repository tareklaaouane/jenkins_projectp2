pipeline {
  agent any

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/tareklaaouane/jenkins_project.git'
      }
    }

    stage('Check tools') {
      steps {
        sh '''
          java -version
          mvn -version
        '''
      }
    }

    stage('Build') {
      steps {
        sh 'mvn -B clean package -DskipTests'
      }
    }

    stage('Test') {
      steps {
        sh 'mvn -B test'
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
        }
      }
    }

    stage('Deploy (Local)') {
      steps {
        sh '''
          mkdir -p deploy
          cp -v target/*.jar deploy/ || cp -v target/*.war deploy/
        '''
      }
    }
  }
}
