pipeline {
    agent any
    environment {
        registry = "docker.io"
        dockerImage = ""
    stages {
        stage('Git Checkout') {
            steps {
               git branch: 'master', credentialsId: 'git-credentials', url: 'https://github.com/itsFarhanPatel/NodeJSProject.git'
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
                    // Login to Docker Hub using --password-stdin
                    withCredentials([usernamePassword(credentialsId: 'my-dockerhub-credentials', passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                    }

                    // Tag and push the Docker image
                    docker.withRegistry('https://index.docker.io/v1/', 'my-dockerhub-credentials') {
                        dockerImage.push('latest')
                    }

                    // Example: Deploy to EC2
                    sshagent(['ec2-ssh-credentials']) {
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.193.91.162 docker run -d -p 3000:3000 myapp:latest'
                    }
                }
            }
        }
    }
}
}
