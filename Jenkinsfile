pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'  // ID of Docker Hub credentials stored in Jenkins
        BACKEND_IMAGE_NAME = 'saloni1224/backend'  // Docker Hub repository name for backend
        FRONTEND_IMAGE_NAME = 'saloni1224/frontend'  // Docker Hub repository name for frontend
        DOCKER_TAG = "latest"  // Tag for the Docker images
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Clone the repository from GitHub
                git branch: 'main', url: 'https://github.com/Saloni-singh22/SRM-Batch-3-Week-1.git'
            }
        }

        stage('Build Backend Docker Image') {
            steps {
                script {
                    dockerImageBackend = docker.build("${env.BACKEND_IMAGE_NAME}:${env.DOCKER_TAG}", "./backend")
                }
            }
        }

        stage('Build Frontend Docker Image') {
            steps {
                script {
                    dockerImageFrontend = docker.build("${env.FRONTEND_IMAGE_NAME}:${env.DOCKER_TAG}", "./client")
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${env.DOCKER_CREDENTIALS_ID}") {
                        echo "Logged in to Docker Hub"
                    }
                }
            }
        }

        stage('Push Backend Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${env.DOCKER_CREDENTIALS_ID}") {
                        dockerImageBackend.push()
                    }
                }
            }
        }

        stage('Push Frontend Docker Image') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', "${env.DOCKER_CREDENTIALS_ID}") {
                        dockerImageFrontend.push()
                    }
                }
            }
        }

        stage('Post-build Cleanup') {
            steps {
                script {
                    // Clean up local Docker images to save space
                    sh "docker rmi ${env.BACKEND_IMAGE_NAME}:${env.DOCKER_TAG}"
                    sh "docker rmi ${env.FRONTEND_IMAGE_NAME}:${env.DOCKER_TAG}"
                }
            }
        }
    }

    post {
        always {
            cleanWs() // Clean up the workspace after the build
        }
        success {
            echo 'Docker images for both backend and frontend successfully built and pushed to Docker Hub!'
        }
        failure {
            echo 'Build failed. Please check the logs for more details.'
        }
    }
}
