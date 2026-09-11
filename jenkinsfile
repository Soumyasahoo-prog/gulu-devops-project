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
                sh 'docker build -t gulu-website:v1 .'
            }
        }

    }
}

