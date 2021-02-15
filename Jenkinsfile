pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Iniciando o build da aplicação'
        sh 'dotnetBuild .'
      }
    }

  }
}