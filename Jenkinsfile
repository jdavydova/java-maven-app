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
  }
} 
