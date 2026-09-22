pipeline {
    agent any

    environment {
        IMAGE_NAME = 'deepu09567/staticwebsite_pipleline'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/deepubhakuni5-create/staticwebsite-pipeline.git'
            }
        }

        stage('Docker Build') {
            steps {
                bat '''
                    docker build -t %IMAGE_NAME%:latest .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat '''
                        echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USER% --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                bat '''
                    docker push %IMAGE_NAME%:latest
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                bat '''
                    docker stop staticwebsite 2>NUL || exit 0
                    docker rm staticwebsite 2>NUL || exit 0
                    docker pull %IMAGE_NAME%:latest
                    docker run -d --name staticwebsite -p 8080:80 %IMAGE_NAME%:latest
                '''
            }
        }
    }
}
