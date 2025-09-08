pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'abhishekpol'                     // Your Docker Hub username
        DOCKERHUB_PASS = credentials('dockerhub-credentials') // ID of secret in Jenkins
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/Abhishekpol89/Resume-website-.git'
            }
        }

        stage('Docker Login') {
            steps {
                sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t abhishekpol/resume-app:latest .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push abhishekpol/resume-app:latest'
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                docker stop resume-app || true
                docker rm resume-app || true
                docker run -d -p 8085:80 --name resume-app abhishekpol/resume-app:latest
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline completed successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs for errors.'
        }
    }
}
