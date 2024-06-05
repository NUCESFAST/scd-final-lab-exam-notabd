pipeline {
    agent any

    environment {
        registry = ""
        DOCKER_CREDENTIALS = ''
    }

    stages {
        stage('Build Docker Images') {
            steps {
                script {
                   // Build auth-service Docker image
                    dir('Auth') {
                        dockerAuth = docker.build("${registry}:auth-service-${BUILD_NUMBER}")
                    }

                    // Build classroom-service Docker image
                    dir('Classrooms') {
                        dockerClassroom = docker.build("${registry}:classroom-service-${BUILD_NUMBER}")
                    }

                    // Build post-service Docker image
                    dir('Post') {
                        dockerPost = docker.build("${registry}:post-service-${BUILD_NUMBER}")
                    }

                    // Build event-bus Docker image
                    dir('event-bus') {
                        dockerEventBus = docker.build("${registry}:event-bus-${BUILD_NUMBER}")
                    }

                    // Build client Docker image
                    dir('client') {
                        dockerClient = docker.build("${registry}:client-${BUILD_NUMBER}")
                    }

                }
            }
        }
        stage('Push Docker Images') {
            steps {
                script {
                    // Push auth-service Docker image
                   docker.withRegistry('', DOCKER_CREDENTIALS) {
                        // Push the built image to Docker Hub
                        dockerAuth.push()
                        dockerClassroom.push()
                        dockerPost.push()
                        dockerEventBus.push()
                        dockerClient.push()
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Deploy using Docker Compose
                    sh 'docker-compose up -d'
                }
            }
        }
    }

    post {
        always {
            // Cleanup
            script {
                // Stop and remove containers after use
                sh 'docker-compose down'
            }
        }
    }
}
