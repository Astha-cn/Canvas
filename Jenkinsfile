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
                '''
            }
        }

        stage('Install Docker') {
            steps {
                sh '''
                set -e

                if ! command -v docker >/dev/null 2>&1; then
                  echo "Docker not found. Installing Docker..."

                  sudo apt-get update -y
                  sudo apt-get install -y \
                    ca-certificates \
                    curl \
                    gnupg \
                    lsb-release

                  curl -fsSL https://get.docker.com | sudo sh

                  sudo usermod -aG docker jenkins
                  sudo systemctl enable docker
                  sudo systemctl start docker
                else
                  echo "Docker already installed"
                fi

                docker --version
                '''
            }
        }

        stage('Install Docker Compose') {
            steps {
                sh '''
                set -e

                if ! docker compose version >/dev/null 2>&1; then
                  echo "Docker Compose not found. Installing..."

                  sudo mkdir -p /usr/local/lib/docker/cli-plugins
                  sudo curl -SL https://github.com/docker/compose/releases/download/v2.25.0/docker-compose-linux-x86_64 \
                    -o /usr/local/lib/docker/cli-plugins/docker-compose

                  sudo chmod +x /usr/local/lib/docker/cli-plugins/docker-compose
                else
                  echo "Docker Compose already installed"
                fi

                docker compose version
                '''
            }
        }

        stage('Deploy Containers') {
            steps {
                sh '''
                set -e
                cd "${WORKSPACE_DIR}"

                echo "Stopping existing containers (if any)..."
                docker compose down || true

                echo "Building & starting containers..."
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
