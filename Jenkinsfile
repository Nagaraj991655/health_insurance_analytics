pipeline {
    agent any

    stages {

        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Cleanup Environment') {
            steps {
                sh '''
                docker compose down --remove-orphans || true

                docker stop $(docker ps -aq) || true

                docker rm -f $(docker ps -aq) || true

                docker network prune -f || true

                docker container prune -f || true

                docker image prune -f || true
                '''
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                docker compose build
                '''
            }
        }

        stage('Deploy Application') {
            steps {
                sh '''
                docker compose up -d
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                sh '''
                docker compose ps
                docker ps
                '''
            }
        }
    }

    post {

        success {
            echo 'Deployment Successful'
        }

        failure {
            echo 'Deployment Failed'

            sh '''
            docker ps -a || true
            docker compose logs || true
            '''
        }
    }
}
