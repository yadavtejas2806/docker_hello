pipeline {
    agent any

    environment {
        IMAGE_NAME = "dipali1904/my-web-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/DipaliGhadage1904/my-web-app.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat """
                    docker build -t %IMAGE_NAME%:%IMAGE_TAG% .
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    bat """
                        docker logout
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    """
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                bat """
                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }

        stage('Deploy Container') {
            steps {
                bat """
                    docker rm -f web-container || echo Container not running
                    docker run -d --name web-container -p 7070:80 %IMAGE_NAME%:%IMAGE_TAG%
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline finished. Visit http://localhost:7070"
        }
    }
}
