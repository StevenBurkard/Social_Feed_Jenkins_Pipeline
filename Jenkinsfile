pipeline {
    agent any

    environment {
        def nodejsTool = tool name: 'node-20-tool', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
        def dockerTool = tool name: 'docker-latest-tool', type: 'org.jenkinsci.plugins.docker.commons.tools.DockerTool'
        PATH = "${nodejsTool}/bin:${dockerTool}/bin:${env.PATH}"
    }

    stages {

        stage('Install Dependencies') {
            steps {
                sh '''
                    echo "Installing Dependencies..."
                    npm install
                '''
            }
        }
        stage('Create Optimized React Build') {
            steps {
                sh '''
                    echo "Packaging the application..."
                    npm run-script build
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                sh """
                    echo "Building Docker Images..."
                    docker build -t stevenburkard/social-feed-jenkins-pipeline:$BUILD_NUMBER .
                """
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'personal-docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh """
                    echo "Pushing Docker images to Docker Hub..."
                    docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}
                    docker push stevenburkard/social-feed-jenkins-pipeline:$BUILD_NUMBER
                """
                }
            }
        }
    }
}