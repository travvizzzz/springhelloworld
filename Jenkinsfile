pipeline {
    agent any

    tools {
        maven "maven3.9"
    }

    environment {
        DOCKER_REPO = "travvizzz/spring-helloworld"
        DOCKER_HOST_PORT = "8081"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/travvizzzz/springhelloworld.git'
            }
        }

        stage('Build Jar') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def imageTag = env.BUILD_NUMBER

                    sh """
                    export DOCKER_BUILDKIT=0
                    export COMPOSE_DOCKER_CLI_BUILD=0

                    docker build --no-cache -t ${DOCKER_REPO}:${imageTag} .
                    docker tag ${DOCKER_REPO}:${imageTag} ${DOCKER_REPO}:latest
                    """

                    env.IMAGE_TAG = imageTag
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                echo "Running container on port ${DOCKER_HOST_PORT}..."

                sh """
                docker stop spring-helloworld || true
                docker rm spring-helloworld || true

                docker run -d \
                  --name spring-helloworld \
                  -p ${DOCKER_HOST_PORT}:8080 \
                  ${DOCKER_REPO}:${IMAGE_TAG}
                """
            }
        }
    }

    post {
        always {
            echo "✅ Pipeline finished."
        }
        success {
            echo "🚀 App running at http://localhost:${DOCKER_HOST_PORT}/"
        }
        failure {
            echo "❌ Pipeline failed."
        }
    }
}
