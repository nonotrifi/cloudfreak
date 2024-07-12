pipeline {
    agent any
    
    tools {
        maven 'maven3'
        jdk 'JDK8'
    }
    
    stages {
        stage('Build Maven Project') {
            steps {
                sh 'mvn clean install package'
            }
        }
        
        stage('Copy Artifact') {
            steps {
                sh 'cp -r target/*.jar docker'
            }
        }
         
        stage('Build Docker Image') {
            steps {
                // Build Docker image
                def customImage = docker.build('initsixcloud/petclinic', "./docker")
                
                // Push Docker image to Docker Hub
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                    customImage.push("${env.BUILD_NUMBER}")
                }
            }
        }
    }
}
