pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'namdevmunde/train-schedule'
        DOCKER_CREDENTIALS_ID = '529cffdb-6d85-4c0d-b087-dd9194f9fcb8' // Jenkins credentials ID
    }

    stages {
        stage('Clone Repo') {
            steps {
                git 'https://github.com/MundeNamdev/cicd-pipeline-train-schedule-autodeploy'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${DOCKER_IMAGE}:${BUILD_NUMBER}")
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('', "${DOCKER_CREDENTIALS_ID}") {
                        dockerImage.push()
                        dockerImage.push('latest')
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat """
                kubectl set image deployment/train-schedule train-schedule=namdevmunde/train-schedule:%BUILD_NUMBER% --record
                """
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
