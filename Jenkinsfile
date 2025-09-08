pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'abhishekpol'
    }

    stages {
        stage('Checkout') {
            steps { 
                // Explicitly checkout main branch
                git branch: 'main', url: 'https://github.com/Abhishekpol89/resume-project-devops1.git'
            }
        }

        stage('Build Docker Image') {
            steps { 
                sh 'docker build -t $DOCKERHUB_USER/devops-resume:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                // Use withCredentials safely to pass secret
                withCredentials([string(credentialsId: 'dockerhub-credentials', variable: 'DOCKERHUB_PASS')]) {
                    sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
                    sh 'docker push $DOCKERHUB_USER/devops-resume:latest'
                }
            }
        }

        stage('Deploy') {
            steps {
                // Stop and remove old container if exists, then run new one
                sh '''
                    docker stop resume-app || true
                    docker rm resume-app || true
                    docker run -d -p 8085:80 --name resume-app $DOCKERHUB_USER/devops-resume:latest
                '''
            }
        }
    }

    post {
        always {
            // Optional: logout from DockerHub after push
            sh 'docker logout'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed! Check logs for errors.'
        }
    }
}
