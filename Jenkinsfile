pipeline {
    agent any
    tools{
        maven "maven3.9"
    }
    environment {
        DOCKER_REPO = "travvizzz/spring-helloworld"
        APP_JAR = "target\\Spring-Html-0.0.1-SNAPSHOT.jar"
        DOCKER_CREDENTIALS_ID = "dockerhub-credentials"
        DOCKER_HOST_PORT = "8081"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url:  'https://github.com/travvizzzz/SpringHelloWorld.git'
            }
        }

        stage('Build Jar') {
            steps {
                bat 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image and tag it with build number
                    def imageTag = "${env.BUILD_NUMBER}"
                    bat "docker build -t ${DOCKER_REPO}:${imageTag} ."
                    bat "docker tag ${DOCKER_REPO}:${imageTag} ${DOCKER_REPO}:latest"
                    env.IMAGE_TAG = imageTag
                }
            }
        }

       
       stage('Run Docker Container') {
        steps {
            echo "Running container locally (port 8082)..."
            bat """
                docker stop spring-html  true
                docker rm spring-html  true
                docker run -d --name spring-html -p 8081:8080 ${DOCKER_REPO}:${env.IMAGE_TAG}
            """
        }
    }

    }

   post {
          always {
              echo "✅ Pipeline finished."
          }
          success {
             echo "Pipeline succeeded! App running at http://localhost:${env.DOCKER_HOST_PORT}/"
          }
          failure {
              echo "Pipeline failed."
          }
      }
}
