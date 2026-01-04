pipeline {
    agent any

    environment {
        WORKSPACE_DIR = "${env.WORKSPACE}"
    }

    stages {

        stage('System Info') {
            steps {
                sh '''
                set -e
                echo "Running on host: $(hostname)"
                echo "User: $(whoami)"
                uname -a
                docker --version
                docker compose version
                '''
            }
        }

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }

        stage('Deploy Containers') {
            steps {
                sh '''
                set -e
                cd "${WORKSPACE_DIR}"

                echo "Stopping existing containers (if any)..."
                docker compose down || true

                echo "Building and starting containers..."
                docker compose up -d --build

                echo "Running containers:"
                docker ps
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment completed successfully!"
        }
        failure {
            echo "❌ Deployment failed. Check Jenkins logs."
        }
    }
}
