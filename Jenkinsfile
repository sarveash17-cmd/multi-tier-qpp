pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                bat 'docker build -t multi-tier-backend:latest backend'
            }
        }

        stage('Build Frontend Image') {
            steps {
                bat 'docker build -t multi-tier-frontend:latest frontend'
            }
        }

    }

    post {
        success {
            echo 'Docker images built successfully'
        }

        failure {
            echo 'Build failed'
        }
    }
}