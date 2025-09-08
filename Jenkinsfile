pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'abhishekpol'
        DOCKERHUB_PASS = credentials('dockerhub-credentials')
    }

    stages {
        stage('Checkout') {
            steps { 
                git branch: 'main', url: 'https://github.com/Abhishekpol89/resume-project-devops1.git'
            }
        }

        stage('Build Docker Image') {
            steps { 
                sh "docker build -t $DOCKERHUB_USER/devops-resume:latest ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                sh "echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin"
                sh "docker push $DOCKERHUB_USER/devops-resume:latest"
            }
        }

        stage('Deploy') {
            steps {
                sh "docker stop resume-app || true && docker rm resume-app || true"
                sh "docker run -d -p 8085:80 --name resume-app $DOCKERHUB_USER/devops-resume:latest"
            }
        }
    }
}
