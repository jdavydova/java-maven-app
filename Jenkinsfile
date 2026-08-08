pipeline {
  agent any

  environment {
    ANSIBLE_SERVER = "13.60.220.188"
  }

  stages {

    stage("copy files to ansible server") {
      steps {
        script {
          echo "copying all necessary files to ansible control node"

          sshagent(['ansible-server-key']) {
            sh """
              scp -o StrictHostKeyChecking=no \
                ansible/* \
                ec2-user@${ANSIBLE_SERVER}:~/
            """

            withCredentials([
              sshUserPrivateKey(
                credentialsId: 'ec2-servers-key',
                keyFileVariable: 'EC2_KEY',
                usernameVariable: 'EC2_USER'
              )
            ]) {
              sshagent(['ansible-server-key']) {
                sh """
                  ssh -o StrictHostKeyChecking=no \
                    ec2-user@${ANSIBLE_SERVER} \
                    'mkdir -p ~/.ssh && chmod 700 ~/.ssh'

                  scp -o StrictHostKeyChecking=no \
                    "\$EC2_KEY" \
                    ec2-user@${ANSIBLE_SERVER}:~/.ssh/ansible-jenkins.pem

                  ssh -o StrictHostKeyChecking=no \
                    ec2-user@${ANSIBLE_SERVER} \
                    'chmod 600 ~/.ssh/ansible-jenkins.pem'
                """
              }
            }
          }
        }
      }
    }

    stage("execute ansible playbook") {
      steps {
        script {
          echo "calling ansible playbook to configure ec2 instances"

          def remote = [:]
          remote.name = "ansible-server"
          remote.host = ANSIBLE_SERVER
          remote.allowAnyHosts = true

          withCredentials([
            sshUserPrivateKey(
              credentialsId: 'ansible-server-key',
              keyFileVariable: 'ANSIBLE_KEY',
              usernameVariable: 'ANSIBLE_USER'
            )
          ]) {
            remote.user = ANSIBLE_USER
            remote.identityFile = ANSIBLE_KEY

            sshCommand remote: remote, command: "ansible-playbook my-playbook.yaml"
          }
        }
      }
    }

  }
}