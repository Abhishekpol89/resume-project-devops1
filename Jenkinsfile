pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'abhishekpol'  // your DockerHub username
    }

    stages {

        stage('Checkout Code') {
            steps { 
                echo "🔄 Checking out code from GitHub..."
                git 'https://github.com/Abhishekpol89/resume-project-devops1.git'
            }
        }

        stage('Docker Login') {
            steps {
                echo "🔑 Logging into Docker Hub..."
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', 
                                                  usernameVariable: 'DOCKER_USER', 
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "📦 Building Docker image..."
                sh 'docker build -t $DOCKERHUB_USER/devops-resume:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "🚀 Pushing Docker image to Docker Hub..."
                sh 'docker push $DOCKERHUB_USER/devops-resume:latest'
            }
        }

        stage('Deploy Container') {
            steps {
                echo "⚡ Deploying Docker container..."
                sh '''
                    docker stop resume-app || true
                    docker rm resume-app || true
                    docker run -d -p 8085:80 --name resume-app $DOCKERHUB_USER/devops-resume:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs for errors."
        }
    }
}
