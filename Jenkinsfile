pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'abhishekpol'                     // Your Docker Hub username
        DOCKERHUB_PASS = credentials('dockerhub-credentials') // ID of secret in Jenkins
        IMAGE_NAME = 'abhishekpol/resume-app:latest'        // Docker image name
        CONTAINER_NAME = 'resume-app'                       // Container name on EC2
        APP_PORT = '8085'                                   // Port to expose
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo '🔄 Checking out code from GitHub...'
                git branch: 'main', url: 'https://github.com/Abhishekpol89/Resume-website-.git'
            }
        }

        stage('Docker Login') {
            steps {
                echo '🔑 Logging into Docker Hub...'
                sh 'echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "📦 Building Docker image: $IMAGE_NAME"
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                echo "⬆️ Pushing Docker image to Docker Hub: $IMAGE_NAME"
                sh 'docker push $IMAGE_NAME'
            }
        }

        stage('Deploy Container') {
            steps {
                echo "🚀 Deploying container: $CONTAINER_NAME on port $APP_PORT"
                sh """
                    # Stop and remove existing container if running
                    if [ \$(docker ps -q -f name=$CONTAINER_NAME) ]; then
                        docker stop $CONTAINER_NAME
                        docker rm $CONTAINER_NAME
                    fi

                    # Run new container
                    docker run -d -p $APP_PORT:80 --name $CONTAINER_NAME $IMAGE_NAME
                """
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
