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
                sh 'docker build -t soumyasahoo2002/gulu-website:v1 .'
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
        sh 'docker push soumyasahoo2002/gulu-website:v1'
    }
}

stage('Deploy to Application EC2') {
    steps {
        withCredentials([sshUserPrivateKey(
            credentialsId: 'app-ec2-ssh',
            keyFileVariable: 'SSH_KEY',
            usernameVariable: 'SSH_USER'
        )]) {
            sh '''
                ssh -o StrictHostKeyChecking=no -i "$SSH_KEY" "$SSH_USER"@172.31.29.182 "
                    docker pull soumyasahoo2002/gulu-website:v1
                    docker rm -f gulu-web || true
                    docker run -d --name gulu-web -p 8080:80 soumyasahoo2002/gulu-website:v1
                "
            '''
        }
    }
}

