pipeline {
    agent any



    stages {
        stage('Validate Compose') {
            steps {
                bat 'docker compose config'
            }
        }

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

        stage('Scan Backend') {
            steps {
                bat 'trivy image multi-tier-backend:latest'
            }
        }

        stage('Scan Frontend') {
            steps {
                bat 'trivy image multi-tier-frontend:latest'
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