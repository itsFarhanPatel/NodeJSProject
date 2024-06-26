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
                            sh "docker build -t itsfarhanpatel/myappnew:latest ."
                    }
               }
            }
        }
        stage('Push Docker Image') {
            steps {
               script {
                   withDockerRegistry(credentialsId: 'my-dockerhub-credentials', toolName: 'docker') {
                            sh "docker push itsfarhanpatel/myappnew:latest"
                    }
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

                    // Push the Docker image to Docker Hub
                    docker.withRegistry('', 'my-dockerhub-credentials') {
                        dockerImage.push('latest')
                    }

                    // Deploy to EC2 instance
                    sshagent(['ec2-ssh-credentials']) {
                        sh 'ssh -o StrictHostKeyChecking=no ubuntu@54.193.91.162 docker run -d -p 3000:3000 itsfarhanpatel/myappnew:latest'
                    }
                }
            }
        }
    }
}
