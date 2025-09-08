pipeline {
    agent any

    environment {
        // DOCKERHUB_USER is now taken from credentials
        DOCKERHUB_REPO = 'devops-resume'  // Docker image name
    }

    stages {

        stage('Checkout Code') {
            steps { 
                echo "🔄 Checking out code from GitHub..."
                git branch: 'main', url: 'https://github.com/Abhishekpol89/resume-project-devops1.git'
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
                sh 'docker build -t abhishekpol/devops-resume:latest .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                echo "🚀 Pushing Docker image to Docker Hub..."
                sh 'docker push abhishekpol/devops-resume:latest'
            }
        }

        stage('Deploy Container') {
            steps {
                echo "⚡ Deploying Docker container..."
                sh '''
                    docker stop resume-app || true
                    docker rm resume-app || true
                    docker run -d -p 8085:80 --name resume-app $DOCKER_USER/$DOCKERHUB_REPO:latest
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Pipeline completed successfully!"
            sh 'docker logout'
        }
        failure {
            echo "❌ Pipeline failed! Check logs for errors."
            sh 'docker logout || true'
        }
    }
}
