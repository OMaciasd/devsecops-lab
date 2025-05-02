pipeline {
  agent any

  environment {
    SONARQUBE_URL = 'http://sonarqube:9000'
    ZAP_URL       = 'http://zap:8090'
  }

  stages {
    stage('Build & Test App') {
      steps {
        echo 'Nada que compilar en Juice Shop, solo arrancar contenedor...'
      }
    }

    stage('SAST con SonarQube') {
      steps {
        // Instala sonar-scanner en el agente o usa un contenedor con el scanner
        sh """
          docker run --rm \
            --network devsecops-lab_default \
            -v \$(pwd)/app:/usr/src \
            sonarsource/sonar-scanner-cli \
            -Dsonar.host.url=${SONARQUBE_URL} \
            -Dsonar.projectKey=juice-shop \
            -Dsonar.sources=/usr/src
        """
      }
    }

    stage('DAST con OWASP ZAP') {
      steps {
        // Daemon de ZAP ya está corriendo, hacer un escaneo rápido
        sh """
          docker run --rm \
            --network devsecops-lab_default \
            owasp/zap2docker-weekly zap-baseline.py \
            -t http://juice-shop:3000 \
            -r zap_report.html \
            -d
        """
        archiveArtifacts artifacts: 'zap_report.html'
      }
    }
  }

  post {
    always {
      echo 'Limpieza…'
      // Aquí podrías detener contenedores si no los quieres siempre arriba
    }
  }
}
