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
                sh 'echo "Pushing Docker images to Docker Hub..."'

                sh """
                    docker build -t stevenburkard/social-feed-jenkins-pipeline:$BUILD_NUMBER .
                """
            }
        }
        stage('Push Docker Image') {
            steps {
                sh 'echo "Pushing Docker images to Docker Hub..."'

                withCredentials([usernamePassword(credentialsId: 'personal-docker-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh """
                    docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}
                """
                }
                sh "docker push stevenburkard/social-feed-jenkins-pipeline:$BUILD_NUMBER"
            }
        }

        stage('Deploy New Image to AWS EC2'){
            steps{
                sh 'echo "Deploying to EC2 instance..."'

                sshagent(['music-library-linux-kp-ssh-credentials']) {
                    sh """
                        SSH_COMMAND="ssh -o StrictHostKeyChecking=no ubuntu@18.218.11.221"
                        \$SSH_COMMAND "docker stop hosted-react-app && docker rm hosted-react-app"
                        \$SSH_COMMAND "docker pull stevenburkard/social-feed-jenkins-pipeline:$BUILD_NUMBER"
                        \$SSH_COMMAND "docker run -d -p 80:80 --name hosted-react-app stevenburkard/social-feed-jenkins-pipeline:$BUILD_NUMBER"
                    """
                }
            }
        }
    }
}