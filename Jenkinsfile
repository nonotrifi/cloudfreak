pipeline {
    agent any
    tools {
        maven 'maven3'
        jdk 'JDK8'
    }
    stages {      
        stage('Build maven') {
            steps { 
                sh 'pwd'      
                sh 'mvn clean install package'
            }
        }
        
        stage('Copy Artifact') {
            steps { 
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }
         
        stage('Build docker image') {
            steps {
                script {         
                    // Logout and Login to Docker Hub
                    sh 'docker logout'
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin https://registry.hub.docker.com'
                    }

                    // Build and push the Docker image
                    def customImage = docker.build('nonotrifi/petclinic', "./docker") // Ensure the image is tagged with your Docker Hub namespace
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push("${env.BUILD_NUMBER}")
                    }
                }
            }
        }
    }
}
