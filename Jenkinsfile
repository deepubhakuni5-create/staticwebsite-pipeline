pipeline {
    agent any

    environment {
        DOCKERHUB_USERNAME = 'deepu09567'
        IMAGE_NAME = 'deepu09567/staticwebsite_pipleline'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                git branch: 'main',
                    url: 'https://github.com/deepubhakuni5-create/staticwebsite-pipeline.git'
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'

                bat '''
                    docker build -t %IMAGE_NAME%:latest .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                echo 'Logging into Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat '''
                        docker login -u "%DOCKER_USER%" -p "%DOCKER_PASSWORD%"
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing image to Docker Hub...'

                bat '''
                    docker push %IMAGE_NAME%:latest
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying container on Windows machine...'

                bat '''
                    docker stop staticwebsite 2>NUL || exit 0
                    docker rm staticwebsite 2>NUL || exit 0

                    docker pull %IMAGE_NAME%:latest

                    docker run -d ^
                        --name staticwebsite ^
                        -p 8764:80 ^
                        %IMAGE_NAME%:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
            echo 'Website: http://localhost:8764'
        }

        failure {
            echo 'CI/CD Pipeline failed.'
        }
    }
}
