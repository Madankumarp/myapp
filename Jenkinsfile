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
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'npm test || true'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Push Docker Image') {

            steps {

                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin

                    docker push $IMAGE_NAME
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

                    sh '''
                    aws ecs update-service \
                    --cluster myapp-cluster-myapp \
                    --service myapp-task-myapp-service-v3hd6hde \
                    --force-new-deployment
                    '''
                }
            }
        }
    }
}