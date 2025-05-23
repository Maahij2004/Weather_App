pipeline {
    agent any
    
    environment {
        VERSION = "${BUILD_NUMBER}"
        FRONTEND_IMAGE = "maahij14/client-app"
        BACKEND_IMAGE = "maahij14/server-app"
        DOCKER_CREDENTIALS_ID = 'docker_cred'
    }

    stages {
        stage('Clone Repository') {
            steps {
                echo "📥 Cloning repository..."
                git branch: 'main', credentialsId: 'github_cred', url: 'https://github.com/Maahij2004/Weather_App.git'
            }
        }

        stage('Build Frontend & Backend Images') {
            steps {
                script {
                    echo "🔨 Building frontend image: ${FRONTEND_IMAGE}:${VERSION}"
                    docker.build("${FRONTEND_IMAGE}:${VERSION}", './Front')

                    echo "🔨 Building backend image: ${BACKEND_IMAGE}:${VERSION}"
                    docker.build("${BACKEND_IMAGE}:${VERSION}", './Server')
                }
            }
        }

        stage('Push Images to Docker Hub') {
            steps {
                script {
                    echo "📦 Pushing images to Docker Hub..."
                    docker.withRegistry('https://index.docker.io/v1/', DOCKER_CREDENTIALS_ID) {
                        docker.image("${FRONTEND_IMAGE}:${VERSION}").push()
                        docker.image("${BACKEND_IMAGE}:${VERSION}").push()

                        docker.image("${FRONTEND_IMAGE}:${VERSION}").tag('latest')
                        docker.image("${BACKEND_IMAGE}:${VERSION}").tag('latest')
                        docker.image("${FRONTEND_IMAGE}:latest").push()
                        docker.image("${BACKEND_IMAGE}:latest").push()
                    }
                }
            }
        }

        stage('Cleanup') {
            steps {
                echo "🧹 Cleaning up unused Docker images..."
                sh 'docker image prune -f'
            }
        }
    }

    post {
        success {
            echo "✅ Build and push successful! Images tagged with version: ${VERSION}"
        }
        failure {
            echo "❌ Build or push failed. Check logs above for details."
        }
    }
}