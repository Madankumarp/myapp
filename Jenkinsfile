pipeline {

    agent any

    environment {
        IMAGE_NAME = "madankp97/myapp:v1"
    }

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/Madankumarp/myapp.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                bat 'npm test'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Push Docker Image') {

            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat '''
                    docker login -u %DOCKER_USER% -p %DOCKER_PASS%

                    docker push %IMAGE_NAME%
                    '''
                }
            }
        }

        stage('Deploy to ECS') {

            steps {

                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds'
                ]]) {

                    bat '''
                    aws ecs update-service ^
                    --cluster myapp-cluster-myapp ^
                    --service myapp-task-myapp-service-v3hd6hde ^
                    --force-new-deployment
                    '''
                }
            }
        }
    }
}