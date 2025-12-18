pipeline {
  agent any

  environment {
    DEPLOY_DIR = "/home/lucifer/Deploy"
  }

  stages {
    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/tareklaaouane/jenkins_projectp2.git'
        sh 'pwd && ls -la'
      }
    }

    stage('Verify project') {
      steps {
        sh '''
          if [ ! -f pom.xml ]; then
            echo "❌ pom.xml introuvable dans $(pwd)"
            echo "➡️ Si le projet est dans un sous-dossier, utilisez dir('nom_dossier')"
            echo "Contenu du workspace:"
            ls -la
            exit 1
          fi
          echo "pom.xml trouvé"
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
          set -e
          mkdir -p "$WORKSPACE/deploy"
          cp -v **/target/*.jar "$WORKSPACE/deploy/" 2>/dev/null || true
          cp -v **/target/*.war "$WORKSPACE/deploy/" 2>/dev/null || true
          ls -la "$WORKSPACE/deploy" || true
        '''
      }
    }

    }
  }
}
