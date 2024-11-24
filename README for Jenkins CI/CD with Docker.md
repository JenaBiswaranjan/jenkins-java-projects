Project Overview:

This project demonstrates how to set up a CI/CD pipeline using Jenkins to build, test, and deploy a Java application. The pipeline automates the following processes:

    Clone the Java application from a GitHub repository.
    Build the application using Maven.
    Create a Docker image with the built .war file.
    Push the Docker image to Docker Hub.
    Deploy the Docker image to a container running on a server.

Technologies Used:

    Java: The application is a Java-based web application (WAR file).
    Maven: Used for building the Java project.
    Docker: Used to create a containerized version of the application.
    Jenkins: Used for CI/CD pipeline automation.
    GitHub: Git repository to store the source code.
    Docker Hub: Cloud registry for storing Docker images.

Steps to Set Up the Environment:
1. GitHub Repository:

The source code for the Java application can be found on the GitHub repository:
Repository URL: https://github.com/JenaBiswaranjan/jenkins-java-projects
2. Jenkins Setup:

    Ensure that Jenkins is installed on your machine or server.
    Install the necessary Jenkins plugins:
        Git Plugin (for pulling from GitHub)
        Docker Pipeline Plugin (for Docker integration)
    Create a new Jenkins pipeline and configure it to pull from the GitHub repository.
    Add Docker Hub credentials in Jenkins for pushing the built Docker image.

3. Docker Setup:

Ensure that Docker is installed and running on the machine. If you're using an EC2 instance or similar, ensure the instance has Docker installed.

Install Docker on Amazon Linux 2 (if using an EC2 instance):

sudo yum update -y
sudo yum install docker -y
sudo systemctl start docker
sudo systemctl enable docker

4. Jenkins Pipeline Configuration:

Create a Jenkinsfile in the root of your repository to automate the build and deployment process. Here's a sample pipeline:

pipeline {
    agent any
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub-creds') // Add Docker Hub credentials in Jenkins
        DOCKER_IMAGE = "biswaranjanjena/javadeploy"
    }
    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }
        stage('Build Java Application') {
            steps {
                script {
                    // Build the application (adjust if needed for your project)
                    sh 'mvn clean install'
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_IMAGE .'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    sh 'echo $DOCKER_HUB_CREDENTIALS_PSW | docker login -u $DOCKER_HUB_CREDENTIALS_USR --password-stdin'
                    sh 'docker push $DOCKER_IMAGE'
                }
            }
        }
    }
}

5. Dockerfile:

The Dockerfile is responsible for creating the Docker image. Here is an example Dockerfile for a Java-based application:

FROM tomcat:8-jre11

# Remove any pre-existing web applications
RUN rm -rf /usr/local/tomcat/webapps/*

# Copy the built WAR file into the Tomcat webapps directory
COPY target/*.war /usr/local/tomcat/webapps/ROOT.war

EXPOSE 8080

6. Build and Run the Docker Image:

After Jenkins builds the Docker image, you can run the container using:

docker run -d -p 8082:8082 deployee

This will run the container in detached mode and map port 8082 on the host to port 8082 in the container.
7. Access the Application:

Once the container is running, you can access the application by navigating to:

http://<your-server-ip>:8082

Replace <your-server-ip> with the IP address of the machine running Docker.
