pipeline {
  agent any

  tools {
    maven 'Maven3'
    jdk 'JDK17'
  }

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

    stage('Deploy') {
      steps {
        sh '''
          echo "🚀 Deploy vers ${DEPLOY_DIR}"
          mkdir -p "${DEPLOY_DIR}"

          # Copier JAR/WAR (selon ton projet)
          cp -v target/*.jar "${DEPLOY_DIR}/" 2>/dev/null || true
          cp -v target/*.war "${DEPLOY_DIR}/" 2>/dev/null || true

          echo "✅ Contenu du dossier de déploiement :"
          ls -la "${DEPLOY_DIR}"
        '''
      }
    }
  }
}
