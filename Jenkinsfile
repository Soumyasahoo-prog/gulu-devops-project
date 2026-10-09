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
                sh '''
                    docker build \
                        -t soumyasahoo2002/gulu-website:v1 \
                        -t soumyasahoo2002/gulu-website:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USERNAME',
                    passwordVariable: 'DOCKER_PASSWORD'
                )]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                            -u "$DOCKER_USERNAME" --password-stdin
                    '''
                }
            }
        }

        stage('Docker Push') {
            steps {
                sh '''
                    docker push soumyasahoo2002/gulu-website:v1
                    docker push soumyasahoo2002/gulu-website:${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([sshUserPrivateKey(
                    credentialsId: 'app-ec2-ssh',
                    keyFileVariable: 'SSH_KEY',
                    usernameVariable: 'SSH_USER'
                )]) {
                    sh '''
                        ssh -i "$SSH_KEY" \
                            -o StrictHostKeyChecking=no \
                            "$SSH_USER"@172.31.29.182 \
                            "sudo k3s kubectl set image deployment/gulu-website gulu-website=soumyasahoo2002/gulu-website:${BUILD_NUMBER} &&
                             sudo k3s kubectl rollout status deployment/gulu-website --timeout=180s &&
                             sudo k3s kubectl get pods"
                    '''
                }
            }
        }
    }
}
