pipeline {
    agent any

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

        stage('Build & Push Docker Image') {
            steps {
                echo "📦 Building and pushing Docker image..."
                sh '''
                    docker build -t $DOCKER_USER/devops-resume .
                    docker push $DOCKER_USER/devops-resume
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                echo "⚡ Deploying Docker container..."
                sh '''
                    docker stop resume-app || true
                    docker rm resume-app || true
                    docker run -d -p 8085:80 --name resume-app $DOCKER_USER/devops-resume:latest
                '''
            }
        }
    }

    post {
        always {
            sh 'docker logout || true'
        }
    }
}
