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
                    // Docker logout and login
                    sh 'docker logout'
                    // Login to Docker Hub using Jenkins credentials
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin https://registry.hub.docker.com
                        '''
                    }
                    
                    // Build and push the Docker image
                    def customImage = docker.build('nonotrifi/petclinic', "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push("${env.BUILD_NUMBER}")
                    }
                }
            }
        }
    }
}
