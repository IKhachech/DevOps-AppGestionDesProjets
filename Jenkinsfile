pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "imenekhacheche"
        BACKEND_IMAGE  = "${DOCKERHUB_USER}/devops-backend"
        FRONTEND_IMAGE = "${DOCKERHUB_USER}/devops-frontend"
    }

    stages {

        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker compose build'
                sh 'docker tag devops-appgestiondesprojets-backend ${BACKEND_IMAGE}:latest'
                sh 'docker tag devops-appgestiondesprojets-frontend ${FRONTEND_IMAGE}:latest'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DH_USER',
                    passwordVariable: 'DH_TOKEN'
                )]) {
                    sh 'echo "$DH_TOKEN" | docker login -u "$DH_USER" --password-stdin'
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                sh 'docker push ${BACKEND_IMAGE}:latest'
                sh 'docker push ${FRONTEND_IMAGE}:latest'
            }
        }

        stage('Deploy Stack') {
            steps {
                sh 'docker compose down'
                sh 'docker compose up -d'
            }
        }

        stage('Health Check') {
            steps {
                sh '''
                    for i in $(seq 1 20); do
                        if curl -sf http://localhost:8090/entreprise/all; then
                            echo "Backend OK"
                            exit 0
                        fi
                        echo "Waiting for backend..."
                        sleep 5
                    done
                    echo "Backend not reachable"
                    exit 1
                '''
            }
        }
    }

    post {
        failure {
            sh 'docker compose logs --tail=50 || true'
        }
    }
}
