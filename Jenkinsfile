pipeline {
    agent any

    environment {
        registry = "docker.io"
        dockerImage = "itsfarhanpatel/myappnew"
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
                    sshagent(['ec2-ssh-credentials']) {
                        // Pull the latest image and run it on the EC2 instance
                        sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@54.193.91.162 << EOF
                        docker pull ${registry}/${dockerImage}:latest
                        docker stop myapp || true
                        docker rm myapp || true
                        docker run -d --name myapp -p 3000:3000 ${registry}/${dockerImage}:latest
                        EOF
                        '''
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
