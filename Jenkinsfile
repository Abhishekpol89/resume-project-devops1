pipeline {
    agent any

    environment {
        // Docker Hub username
        DOCKER_USER = 'abhishekpol'
        // Docker Hub token stored in Jenkins credentials (type: Username with password)
        DOCKER_PASS = credentials('dockerhub-credentials')
        IMAGE_NAME = "$DOCKER_USER/devops-resume:latest"
        CONTAINER_NAME = "resume-app"
        HOST_PORT = "8085"
        CONTAINER_PORT = "80"
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
                sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "📦 Building Docker image..."
                sh "docker build -t $IMAGE_NAME ."
            }
        }

        stage('Push Docker Image') {
            steps {
                echo "🚀 Pushing Docker image to Docker Hub..."
                sh "docker push $IMAGE_NAME"
            }
        }

        stage('Deploy Container') {
            steps {
                echo "⚡ Deploying Docker container on EC2..."
                sh """
                    docker stop $CONTAINER_NAME || true
                    docker rm $CONTAINER_NAME || true
                    docker run -d -p $HOST_PORT:$CONTAINER_PORT --name $CONTAINER_NAME $IMAGE_NAME
                """
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
