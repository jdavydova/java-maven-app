pipeline {   
  agent any
  environment {
    ANSIBLE_SERVER = "13.60.220.188"
  }
  stages {
    stage("copy files to ansible server") {
      steps {
        script {
          echo "copying all neccessary files to ansible control node"
          sshagent(['ansible-server-key']) {
            sh "scp -o StrictHostKeyChecking=no ansible/* ec2-user@${ANSIBLE_SERVER}:~/"

            withCredentials([sshUserPrivateKey(credentialsId: 'ec2-servers-key', keyFileVariable: 'keyfile', usernameVariable: 'user')]) {
              sh 'scp $keyfile ec2-user@$ANSIBLE_SERVER:~/ssh-key.pem'
            }
          }
        }
      }
    }
    stage ("execute ansible playbook") {
      steps {
        script {
          echo "calling ansible playbook to configure ec2 instances"
          def remote = [:]
          remote.name = "ansible-server"
          remote.host = "$ANSIBLE_SERVER"
          remote.allowAnyhosts = true
          sshCommand remote: remote, command: "ls -l"

          withCredentials([sshUserPrivateKey(credentialsId: 'ansible-server-key', keyFileVariable: 'keyfile', usernameVariable: 'user')]) {
            remote.user = user
            remote.identityFile = keyfile
            sshCommand remote: remote, command: "ls -la"
          }
        }
      }
    }
  }
} 
