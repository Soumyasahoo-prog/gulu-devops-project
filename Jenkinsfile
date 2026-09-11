pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out Gulu DevOps project'
                checkout scm
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t Soumyasahoo2002/gulu-website:v1 .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh 'echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USERNAME" --password-stdin'
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push Soumyasahoo2002/gulu-website:v1'
            }
        }
    }

