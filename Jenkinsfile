pipeline {
    agent any

    environment {
        IMAGE_NAME = 'deepu09567/staticwebsite_pipleline'

        DOCKER_PATH = 'C:/Users/Ankit/AppData/Local/Programs/DockerDesktop/resources/bin/docker.exe'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
            }
        }

        stage('Check Docker') {
            steps {
                bat '''
                    "%DOCKER_PATH%" --version
                '''
            }
        }

        stage('Docker Build') {
            steps {
                echo 'Building Docker image...'

                bat '''
                    "%DOCKER_PATH%" build -t %IMAGE_NAME%:latest .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                echo 'Logging into Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials-new',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat '''
                        echo %DOCKER_PASSWORD% | "%DOCKER_PATH%" login -u %DOCKER_USER% --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                echo 'Pushing image to Docker Hub...'

                bat '''
                    "%DOCKER_PATH%" push %IMAGE_NAME%:latest
                '''
            }
        }

        stage('Deploy Container') {
            steps {
                echo 'Deploying container...'

                bat '''
                    "%DOCKER_PATH%" stop staticwebsite 2>NUL || exit 0

                    "%DOCKER_PATH%" rm staticwebsite 2>NUL || exit 0

                    "%DOCKER_PATH%" pull %IMAGE_NAME%:latest

                    "%DOCKER_PATH%" run -d --name staticwebsite -p 8080:80 %IMAGE_NAME%:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'CI/CD Pipeline completed successfully!'
            echo 'Website: http://localhost:8080'
        }

        failure {
            echo 'CI/CD Pipeline failed.'
        }
    }
}
