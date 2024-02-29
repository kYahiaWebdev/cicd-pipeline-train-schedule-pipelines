pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Running build'
        sh './gradlew build --no-daemon'
        archiveArtifacts artifacts: 'dist/trainSchedule.zip'
      }
    }

    stage('Build Docker Image') {
      when {
        branch 'master'
      }
      steps {
        script {
          app = docker.build("kyahia/train-schedule")
          app.inside {
            sh 'echo $(curl localhist:8080)'
          }
        }
      }
    }
  }
}
