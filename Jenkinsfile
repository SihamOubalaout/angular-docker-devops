pipeline {
    agent any
    tools {
        maven 'Maven' // Ensure Maven is installed in Jenkins
    }
    environment {
        // Adding Docker to PATH for Windows
        PATH = "C:\\WINDOWS\\SYSTEM32;C:\\Program Files\\Docker\\Docker\\resources\\bin"
        
        // Use Jenkins credentials to securely manage Docker Hub credentials
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        
        // Set Docker image name using the Docker Hub username and build number
        DOCKER_IMAGE = "oubalaoutsiham/awwin:${env.BUILD_NUMBER}"
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
                    // Build the Docker image using the Docker Hub username
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
                    // Use Jenkins credentials securely for Docker Hub login
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        // Securely pass the password using stdin
                        bat "echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USERNAME} --password-stdin"
                    }
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    // Push the Docker image to Docker Hub
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        stage('Deploy Backend') {
            steps {
                script {
                    // Example of deploying the Spring backend application (customize as needed)
                    bat "docker run -d -p 8081:8081 spring-app"
                }
            }
        }

        stage('Deploy Frontend') {
            steps {
                script {
                    // Deploy the Angular frontend (customize as needed)
                    bat "docker run -d -p 4201:80 ${DOCKER_IMAGE}"
                }
            }
        }
    }

    post {
        always {
            // Ensure logout from Docker Hub after pipeline completes
            bat "docker logout"
        }
    }
}
