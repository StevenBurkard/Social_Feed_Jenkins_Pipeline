pipeline {
    agent any

    stages {

        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "Installing Dependencies..."
                '''
            }
        }
        stage('Create Optimized React Build') {
            steps {
                sh '''
                    echo "Packaging the application..."
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                sh '''
                    echo "Building Docker Images..."
                '''
            }
        }
        stage('Push Docker Image') {
            steps {
                sh '''
                    echo "Pushing Docker Images to Docker Hub..."
                '''
            }
        }
    }
}