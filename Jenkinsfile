pipeline {
    agent any
    tools {
        maven 'Maven' // Ensure Maven is installed in Jenkins
    }
    environment {
        // Adding Docker to PATH for Windows
        PATH = "C:\\WINDOWS\\SYSTEM32;C:\\Program Files\\Docker\\Docker\\resources\\bin"
        // Replace 'docker-hub-credentials' with your Docker Hub credentials ID in Jenkins
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        // Set Docker image name using environment variable for Docker Hub username
        DOCKER_IMAGE = "oubalaoutsiham/angular-17-crud-app:${env.BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image using the Dockerfile
                    bat "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Security Scan with Grype') {
            steps {
                script {
                    // Run a Grype security scan on the built Docker image
                    bat "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock anchore/grype ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Login to DockerHub') {
            steps {
                script {
                    // Use Jenkins credentials to securely login to Docker Hub
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        bat "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    // Push the Docker image to Docker Hub repository
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                script {
                    // Deploy the Angular frontend (customize port as needed)
                    bat "docker run -d -p 4201:80 ${DOCKER_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            // Ensure Docker logout after the pipeline completes
            bat "docker logout"
        }
    }
}
