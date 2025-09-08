pipeline {
    agent any

    stages {
        stage('Checkout Code') {
            steps {
                echo "🔄 Checking out code..."
                git branch: 'main', url: 'https://github.com/Abhishekpol89/resume-project-devops1.git'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
            }
        }

        stage('Build & Push Docker Image') {
            steps {
                sh '''
                    echo "📦 Building Docker image..."
                    docker build -t $DOCKER_USER/devops-resume:latest .
                    echo "🚀 Pushing Docker image..."
                    docker push $DOCKER_USER/devops-resume:latest
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                sh '''
                    echo "⚡ Deploying container..."
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
        success {
            echo "✅ Pipeline finished successfully!"
        }
        failure {
            echo "❌ Pipeline failed!"
        }
    }
}
