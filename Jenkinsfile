pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
               git branch: 'master', credentialsId: 'git-credentials', url: 'https://github.com/itsFarhanPatel/NodeJSProject.git'
            }
        }   
         stage('Build & Tag Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'my-dockerhub-credentials', toolName: 'docker') {
                            sh "docker build -t itsfarhan/myapp:latest ."
                    }
               }
            }
        }
        stage('Push Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'my-dockerhub-credentials', toolName: 'docker') {
                            sh "docker push itsfarhanpatel/myapp:latest"
                    }
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
