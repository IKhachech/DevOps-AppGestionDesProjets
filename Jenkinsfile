pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps { checkout scm }
        }

        stage('Build Docker Images') {
            steps { sh 'docker compose build' }
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
