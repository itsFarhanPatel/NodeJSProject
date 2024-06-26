pipeline {
    agent any

    stages {
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
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.193.91.162 docker run -d -p 3000:3000 myapp'
                    }
                }
            }
        }
    }
}
