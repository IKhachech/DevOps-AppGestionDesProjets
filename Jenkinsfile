pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps { checkout scm }
        }
        stage('Build Backend') {
            steps {
                dir('backend') { sh 'mvn -B clean package -DskipTests' }
            }
        }
        stage('Test Backend') {
            steps {
                dir('backend') { sh 'mvn -B test' }
            }
        }
        stage('Build Frontend') {
            steps {
                dir('frontend') {
                    sh 'npm ci'
                    sh 'npm run build'
                }
            }
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
    }
    post {
        always {
            archiveArtifacts artifacts: 'backend/target/*.jar', fingerprint: true, allowEmptyArchive: true
        }
    }
}
