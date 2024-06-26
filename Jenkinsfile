pipeline {
    agent any
    environment {
        EC2_IP = '54.193.91.162'
        SSH_CREDENTIALS_ID = 'ec2-ssh-credentials-id'
        registry = "docker.io"
        dockerImage = "itsfarhanpatel/new-myapp"
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/itsFarhanPatel/NodeJSProject.git'
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}/${dockerImage}")
                }
            }
        }

        stage('Push Docker image') {
            steps {
                script {
                    docker.withRegistry('', 'my-dockerhub-credentials') {
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                script {
                    sshagent(credentials: [SSH_CREDENTIALS_ID]) {
                        sh """
                        ssh -o StrictHostKeyChecking=no ubuntu@${EC2_IP} 'bash -s' < deploy-script.sh
                        """
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
}
