pipeline {
    agent any

    stages {
        stage('Clone repository') {
            steps {
     git clone 'https://github.com/itsFarhanPatel/NodeJSProject.git' 
            }
        }
        stage('Build Docker image') {
            steps {
                script {
                    dockerImage = docker.build("myapp")
                }
            }
        }
        stage('Deploy to EC2') {
            steps {
                script {
                    docker.withRegistry('', 'my-dockerhub-credentials') {
                        dockerImage.push('latest')
                    }
                    sshagent(['ec2-ssh-credentials']) {
                        sh 'ssh -o StrictHostKeyChecking=no ec2-user@<EC2-IP> docker run -d -p 3000:3000 myapp'
                    }
                }
            }
        }
    }
}
