pipeline {
    agent any

    stages {

        stage('Checkout Code') {
            steps {
                echo "🔄 Checking out code from GitHub..."
                git branch: 'main', url: 'https://github.com/Abhishekpol89/resume-project-devops1.git'
            }
        }

        stage('Docker Login & Build/Push') {
            steps {
                // Use your Docker Hub credentials
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "🔑 Logging into Docker Hub..."
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        
                        echo "👀 Confirming Docker username: $DOCKER_USER"
                        
                        echo "📦 Building Docker image..."
                        docker build -t $DOCKER_USER/devops-resume:latest .

                        echo "🚀 Pushing Docker image..."
                        docker push $DOCKER_USER/devops-resume:latest
                    '''
                }
            }
        }

        stage('Deploy Container') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials',
                                                  usernameVariable: 'DOCKER_USER',
                                                  passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo "⚡ Deploying Docker container..."
                        docker stop resume-app || true
                        docker rm resume-app || true
                        docker run -d -p 8085:80 --name resume-app $DOCKER_USER/devops-resume:latest
                    '''
                }
            }
        }
    }

    post {
        always {
            echo "🛑 Logging out from Docker Hub..."
            sh 'docker logout || true'
        }
        success {
            echo "✅ Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed! Check logs for errors."
        }
    }
}
