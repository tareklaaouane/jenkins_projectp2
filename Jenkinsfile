pipeline {
  agent any

  environment {
    IMAGE_NAME = "tp3-java-app:latest"
    CONTAINER_NAME = "tp3-java-container"
    APP_PORT_HOST = "8081"     // Port accessible sur le PC
    APP_PORT_CONTAINER = "8080" // Port exposé par l’app dans Docker
  }

  stages {

    stage('Checkout') {
      steps {
        git branch: 'main', url: 'https://github.com/tareklaaouane/jenkins_projectp2.git'
        sh 'pwd && ls -la'
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

    stage('Docker Build') {
      steps {
        sh 'docker build -t $IMAGE_NAME .'
      }
    }

    stage('Deploy (Local Docker)') {
      steps {
        sh '''
          docker stop $CONTAINER_NAME || true
          docker rm $CONTAINER_NAME || true

          docker run -d \
            --name $CONTAINER_NAME \
            -p ${APP_PORT_HOST}:${APP_PORT_CONTAINER} \
            $IMAGE_NAME

          docker ps | grep $CONTAINER_NAME || true
        '''
      }
    }

    stage('Smoke Test (Local)') {
      steps {
        sh '''
          echo "Testing URL: http://localhost:${APP_PORT_HOST}"
          sleep 3
          curl -I http://localhost:${APP_PORT_HOST} || true
        '''
      }
    }
  }

  post {
    success {
      echo "✅ Déploiement local réussi : http://localhost:${APP_PORT_HOST}"
    }
    failure {
      echo "❌ Pipeline échoué : vérifier les logs Jenkins"
    }
    always {
      archiveArtifacts artifacts: 'target/*.jar', fingerprint: true, allowEmptyArchive: true
    }
  }
}
