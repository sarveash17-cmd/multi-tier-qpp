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

        stage('Scan Backend Image') {
            steps {
                bat '''
                trivy image --severity HIGH,CRITICAL multi-tier-backend
                '''
                }
            }

        stage('Scan Frontend Image') {
            steps {
                bat '''
                trivy image --severity HIGH,CRITICAL multi-tier-frontend
                '''
            }
        }
        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'MydockerhubCreds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    bat '''
                    echo %DOCKER_PASSWORD% | docker login -u %DOCKER_USERNAME% --password-stdin
                    '''
                }
            }
        }

        stage('Push Backend') {
            steps {

                bat '''
                docker tag multi-tier-backend %DOCKER_USER%/multi-tier-backend:v1
                docker push %DOCKER_USER%/multi-tier-backend:v1
                '''
            }
        }

        stage('Push Frontend') {
            steps {

                bat '''
                docker tag multi-tier-frontend %DOCKER_USER%/multi-tier-frontend:v1
                docker push %DOCKER_USER%/multi-tier-frontend:v1
                '''
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