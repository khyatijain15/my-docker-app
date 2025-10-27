pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'myapp:latest'
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/khyatijain15/my-docker-app.git'
            }
        }
        stage('Build App') {
            steps {
                bat 'npm install'
            }
        }
        stage('Docker Build') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }
        stage('Run Container') {
            steps {
                script {
                    bat '''
                        docker stop myapp || exit 0
                        docker rm myapp || exit 0
                        docker run -d --name myapp -p 3000:3000 %DOCKER_IMAGE%
                    '''
                }
            }
        }
    }
    post {
        always {
            echo 'Pipeline finished successfully!'
        }
    }
}
