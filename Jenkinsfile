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

    stage('StagingDeploy') {
      when {
        branch 'master'
      }
      steps {
        withCredentials([usernamePassword(credentialsId: 'servers_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
          sshPublisher (
            failOnError: true,
              continueOnError: false,
              publishers: [
                  sshPublisherDesc(
                    configName: "staging-server",
                    sshCredentials: [
                      username: "$USERNAME",
                      encryptedPassphrase: "$USERPASS"
                    ],
                    transfers: [
                      sshTransfer(
                        sourceFiles: 'dist/trainSchedule.zip',
                        removePrefix: 'dist/',
                        remoteDirectory: '/tmp',
                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule/ && sudo /usr/bin/systemctl start train-schedule'
                      )
                    ]
                  )
              ]
          )
        }
      }
    }

    stage('ProdDeploy') {
      when {
        branch 'master'
      }
      steps {
        input 'Confirm update ?'
        milestone(1)
        withCredentials([usernamePassword(credentialsId: 'servers_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
          sshPublisher (
            failOnError: true,
              continueOnError: false,
              publishers: [
                  sshPublisherDesc(
                    configName: "prod-server",
                    sshCredentials: [
                      username: "$USERNAME",
                      encryptedPassphrase: "$USERPASS"
                    ],
                    transfers: [
                      sshTransfer(
                        sourceFiles: 'dist/trainSchedule.zip',
                        removePrefix: 'dist/',
                        remoteDirectory: '/tmp',
                        execCommand: 'sudo /usr/bin/systemctl stop train-schedule && rm -rf /opt/train-schedule/* && unzip /tmp/trainSchedule.zip -d /opt/train-schedule/ && sudo /usr/bin/systemctl start train-schedule'
                      )
                    ]
                  )
              ]
          )
        }
      }
    }
  }
}
