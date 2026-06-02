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
                        "C:\\Users\\sarve\\AppData\\Local\\Microsoft\\WinGet\\Links\\trivy.exe" image --scanners vuln --severity HIGH,CRITICAL multi-tier-backend
                            '''
                }
            }

        stage('Scan Frontend Image') {
            steps {
                bat '''
                    "C:\\Users\\sarve\\AppData\\Local\\Microsoft\\WinGet\\Links\\trivy.exe" image --scanners vuln --severity HIGH,CRITICAL multi-tier-frontend
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
                withCredentials([
                    usernamePassword(
                        credentialsId: 'MydockerhubCreds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    bat '''
                    docker tag multi-tier-backend %DOCKER_USERNAME%/multi-tier-backend:v1
                    docker push %DOCKER_USERNAME%/multi-tier-backend:v1
                    '''
                }
            }
        }

        stage('Push Frontend') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'MydockerhubCreds',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                 bat '''
                    docker tag multi-tier-frontend %DOCKER_USERNAME%/multi-tier-frontend:v1
                    docker push %DOCKER_USERNAME%/multi-tier-frontend:v1
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