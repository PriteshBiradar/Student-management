```groovy
pipeline {

    agent any

    environment {
        DOCKER_IMAGE = "mangeshc225/student-management"
        DOCKER_TAG   = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                echo '📥 Checking out source code...'
                checkout scm
            }
        }

        stage('Build with Maven') {
            steps {
                echo '🔨 Building Spring Boot application...'

                sh '''
                    chmod +x mvnw || true
                    mvn clean package -DskipTests
                '''
            }
        }

        stage('Docker Build') {
            steps {
                echo '🐳 Building Docker image...'

                sh '''
                    docker build \
                    -t ${DOCKER_IMAGE}:${DOCKER_TAG} \
                    -t ${DOCKER_IMAGE}:latest .
                '''
            }
        }

        stage('Docker Login') {
            steps {
                echo '🔐 Logging in to Docker Hub...'

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                echo '📤 Pushing Docker image to Docker Hub...'

                sh '''
                    docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                    docker push ${DOCKER_IMAGE}:latest
                '''
            }
        }

        stage('Deploy to EC2') {
            steps {
                echo '🚀 Deploying application to EC2...'

                sshagent(['ec2-ssh-key']) {

                    sh '''
                        ssh -o StrictHostKeyChecking=no \
                        ${EC2_USER}@${EC2_HOST} << EOF

                            echo "Pulling latest Docker image..."

                            docker pull ${DOCKER_IMAGE}:latest

                            echo "Stopping old container..."

                            docker stop student-management || true
                            docker rm student-management || true

                            echo "Starting new container..."

                            docker run -d \
                                --name student-management \
                                --restart unless-stopped \
                                -p 8080:8080 \
                                ${DOCKER_IMAGE}:latest

                            echo "Deployment completed!"

                            docker ps

                        EOF
                    '''
                }
            }
        }
    }

    post {

        success {
            echo '✅ CI/CD Pipeline completed successfully!'
            echo "Docker Image: ${DOCKER_IMAGE}:${DOCKER_TAG}"
        }

        failure {
            echo '❌ CI/CD Pipeline failed!'
        }

        always {
            echo "🏁 Jenkins Build #${BUILD_NUMBER} finished."
        }
    }
}
```
