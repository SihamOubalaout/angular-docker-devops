pipeline {
    agent any
    
    environment {
        // Replace 'docker-hub-credentials' with each user's Docker Hub credentials ID
        DOCKERHUB_CREDENTIALS = credentials('docker-hub-credentials')
        // Set Docker image name using environment variable for Docker Hub username
        DOCKER_IMAGE = "${Siham Oubalaout}/angular-17-crud-app:${env.BUILD_NUMBER}"
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
                    // Build the Docker image using the user's Docker Hub username
                    bat "docker build -t angular-17-crud-app ."
                }
            }
        }

        stage('Security Scan with Grype') {
            steps {
                script {
                    // Run a Grype security scan on the built Docker image
                    bat "docker run --rm -v /var/run/docker.sock:/var/run/docker.sock anchore/grype angular-17-crud-app"
                }
            }
        }
        
        stage('Login to DockerHub') {
            steps {
                script {
                    // Login to Docker Hub using the provided credentials
                    bat "docker login -u docker-hub-credentials -p Ensi@s2002"
                }
            }
        }

        stage('Push to DockerHub') {
            steps {
                script {
                    // Push the image to the user's Docker Hub repository
                    bat "docker push ${DOCKER_IMAGE}"
                }
            }
        }

        //stage('Deploy Backend') {
           // steps {
              //  script {
                    // Deploy the Spring backend application (customize port as needed)
                 //   bat "docker run -d -p 8081:8081 spring-app"
              //  }
          //  }
       // }

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
            // Ensure logout from Docker Hub after pipeline completes
            bat "docker logout"
        }
    }
}
